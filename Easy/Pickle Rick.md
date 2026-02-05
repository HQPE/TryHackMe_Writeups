<h1>Pickle Rick
A Rick and Morty CTF. Help turn Rick back into a human!</h1>



<h1>Tarama</h1>
<br>
Bir makineye saldırı yapmadan önce, o makinenin açık olan servislerini ve portlarını öğrenmek gerekir. Bu, nereden başlayacağımızı anlamamızı sağlar. Portlar ve servisler hakkında bilgi almak için Nmap kullanıyoruz:

-sV: Servis versiyonlarını öğrenmek için,
-sC: Yaygın scriptleri çalıştırmak için kullanılır.

<pre>nmap -sV -sC 10.10.x.x</pre>
![1](https://github.com/user-attachments/assets/2c4b9243-26f5-41a6-8473-1a78c72fe272)
<br><br><br><br>

<h1>Web Servisine Erişim</h1>
<br>
Tarama sonucu web sunucusunun açık olduğunu gördük. İlk adım olarak, o servisin çalıştığı port üzerinden web sitesine erişim sağlıyoruz. Böylece servis üzerinde keşif yapabiliriz:<pre> http://10.10.x.x </pre>

![Image](https://github.com/user-attachments/assets/282997e5-783c-4265-901d-11bc7c25fcf7)
<br><br><br><br>

<h1>Sayfa Kaynağı İncelemesi</h1>
<br>
Web sayfalarında genellikle yorum satırları veya gizli bilgiler bırakılabilir. Sayfa kaynağına (Ctrl+U) bakarak, saldırıya veya ipuçlarına yarayacak herhangi bir bilgi var mı diye kontrol ediyoruz.
<br><br>

![Image](https://github.com/user-attachments/assets/ec4771aa-09db-4dca-bfd9-7b574d38f7f8)
<br><br><br><br>


<h1>Gizli Dosya ve Dizinleri Bulma</h1>
<br>
Web sunucusunda, normalde erişim sağlanmayan veya gizli olan dizin ve dosyalar olabilir. Bunlar bazen önemli bilgiler içerebilir. Bunun için Gobuster gibi araçlarla dizin taraması yapıyoruz:
<pre> gobuster dir -u http://10.10.x.x -w /usr/share/wordlists/dirb/common.txt </pre>
-w: Kelime listesi (wordlist) ile dizinleri deniyor

![Image](https://github.com/user-attachments/assets/28c9eee7-821a-495d-b348-5ad0141e58c0)

Bulunan dosyalar:
<ul>
  <li>•/robots.txt </li>
  <li>•/login.php → Giriş paneli</li>
</ul>
<br><br><br><br>



<h1>Giriş Paneli</h1>
<br>
Bulduğumuz dizindeki dosya:
Robots.txt Arama motorlarına yol gösteren, hacker'lara sızma ipucu veren dosya!
<br><br>

![Image](https://github.com/user-attachments/assets/f6cacbe1-87c8-4f5a-ad95-5518c8689142)
<br>

Bulduğumuz bilgilerle giriş yapalım:
<ul>
  <li>Kullanıcı Adı: R1ckRul3s</li>
  <li>Şifre: Wubbalubbadubdub → Giriş paneli</li>
</ul>

![Image](https://github.com/user-attachments/assets/956f1e36-441e-4155-8ad0-d0077791d092)



<br><br><br><br>
<h1>Komut Paneli</h1>
<br>
Giriş yaptıktan sonra komut çalıştırabileceğimiz bir panel açıldı. Bu, hedef makine üzerinde komut çalıştırma izni verdiği için kritik. Buradan sistem hakkında daha fazla bilgi alabiliriz.
Basit komutları deneyelim:
<br><br>

![Image](https://github.com/user-attachments/assets/a35bdee4-104d-44ef-9f3f-a907048ad594)



<h1>Web Sitesinin Dosyalarını İnceleme</h1>
<br>
Dosyalar bize sistem hakkında ipuçları verebilir. Örneğin, içinde şifrenin, malzemelerin veya diğer önemli bilgilerin olduğu dosyalar olabilir.
<pre>ls</pre>

Çıktı:
• Sup3rS3cretPickl3Ingred.txt (henüz okuyamıyoruz)

İçeriğini okumak için:
<pre>Less /var/www/html/Sup3rS3cretPickl3Ingred.txt</pre>
![Image](https://github.com/user-attachments/assets/501a1770-05d8-432f-9f02-0fffe3a756ad)
<br><br><br><br>


<h1>Kullanıcı Dizini ve İkinci Malzeme</h1>
<br>
Home dizinleri, kullanıcılara özel dosyaları içerir. Burada önemli bilgiler veya ikinci malzeme olabilir.
<pre>
ls /home
ls /home/rick
less /home/rick/"second ingredients" 
</pre>

![Image](https://github.com/user-attachments/assets/5821978b-4275-450b-b517-e3dc76b37559)
<br><br><br><br>


<h1>Yetki Yükseltme Kontrolü</h1>
<br>
Sistemdeki yetkilerimizi kontrol etmek, daha fazla erişim elde etmek için önemli. sudo -l komutu ile kullanıcının hangi komutları şifresiz çalıştırabileceğini öğreniriz.

<pre>sudo -l</pre>
Bu komut bize şunu gösterdi:
www-data kullanıcısı şifresiz her komutu sudo ile çalıştırabiliyor!
Bu bir yetki yükseltme açığı.

![Image](https://github.com/user-attachments/assets/9f45b73a-41ea-427a-9cad-a94a1a90b398)

<br><br><br><br>
<h1>Yetkili Root hesabı</h1>
<br>
Elde ettiğimiz bu yetki ile root kullanıcısına ait dosyaları okuyabiliriz.
<pre>sudo less /root/3rd.txt</pre>pre>

![Image](https://github.com/user-attachments/assets/a4f73997-b3d7-48d1-b75a-f940a4df95a7)



<br><br><br><br>
<h1>Answer the questions below</h1>
<ul>
<li>What is the first ingredient that Rick needs?</li>
	mr. meeseek hair
<li>What is the second ingredient in Rick’s potion?</li>
	1 jerry tear
<li>What is the last and final ingredient?</li>
	fleeb juice
</ul>

