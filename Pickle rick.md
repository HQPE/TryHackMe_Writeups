Pickle rick

Tarama
Önce makinede hangi portların açık olduğuna bakalım:

nmap -sV -sC 10.10.x.x

![1](https://github.com/user-attachments/assets/2c4b9243-26f5-41a6-8473-1a78c72fe272)



Tarayıcıdan web sitesini açın:

http://10.10.x.x


![Image](https://github.com/user-attachments/assets/282997e5-783c-4265-901d-11bc7c25fcf7)

Sayfa kaynağına bakın (Ctrl+U) ve şuna benzer bir yorum göreceksiniz:

Kaynak <https://chat.deepseek.com/a/chat/s/27880800-e43f-4865-8d27-c7ac4032cfa2> 
![Image](https://github.com/user-attachments/assets/ec4771aa-09db-4dca-bfd9-7b574d38f7f8)



Gizli Dosyaları Bulma
Gobuster ile gizli dizinleri bulalım:

gobuster dir -u http://10.10.x.x -w /usr/share/wordlists/dirb/common.txt

Kaynak <https://chat.deepseek.com/a/chat/s/27880800-e43f-4865-8d27-c7ac4032cfa2> 

![Image](https://github.com/user-attachments/assets/28c9eee7-821a-495d-b348-5ad0141e58c0)






Bulacağınız önemli dosyalar:
• /robots.txt (içinde: Wubbalubbadubdub yazıyor)
• /login.php (giriş sayfası)

Kaynak <https://chat.deepseek.com/a/chat/s/27880800-e43f-4865-8d27-c7ac4032cfa2> 

![Image](https://github.com/user-attachments/assets/f6cacbe1-87c8-4f5a-ad95-5518c8689142)




Bulduğumuz bilgilerle giriş yapalım:
• Kullanıcı Adı: R1ckRul3s
• Şifre: Wubbalubbadubdub (robots.txt'den)

Kaynak <https://chat.deepseek.com/a/chat/s/27880800-e43f-4865-8d27-c7ac4032cfa2> 


![Image](https://github.com/user-attachments/assets/0842fa0a-8395-4334-96cd-105effb47085)



Komut Paneli
Giriş yaptıktan sonra komut çalıştırabileceğiniz bir panel göreceksiniz.
Basit komutlar deneyelim:

Kaynak <https://chat.deepseek.com/a/chat/s/27880800-e43f-4865-8d27-c7ac4032cfa2> 










İlk Malzeme
Web sitesinin dosyalarını kontrol edelim:
ls 

Bu bize şunları gösterecek:
• Sup3rS3cretPickl3Ingred.txt (henüz okuyamıyoruz)

Şu komutla ilk malzemeyi okuyabiliriz:
bash
Copy
Download
Less /var/www/html/Sup3rS3cretPickl3Ingred.txt
İlk malzeme: mr. meeseek hair

Kaynak <https://chat.deepseek.com/a/chat/s/27880800-e43f-4865-8d27-c7ac4032cfa2> 











Kullanıcı dizinlerine bakalım:


ls /home
ls /home/rick
less /home/rick/"second ingredients"

Less komutu ile açıldı







Yetki yükseltme olup olmadığını kontrol edelim:

Kaynak <https://chat.deepseek.com/a/chat/s/27880800-e43f-4865-8d27-c7ac4032cfa2> 



sudo -l


Bu komut bize şunu gösterdi:

Kaynak <https://chat.deepseek.com/a/chat/s/27880800-e43f-4865-8d27-c7ac4032cfa2> 




User www-data may run the following commands on Linux:
    (ALL) NOPASSWD: ALL
Yani:
✔ www-data kullanıcısı şifresiz her komutu sudo ile çalıştırabiliyor!
✔ Bu bir yetki yükseltme açığı.

Kaynak <https://chat.deepseek.com/a/chat/s/27880800-e43f-4865-8d27-c7ac4032cfa2> 









sudo less /root/3rd.txt








Answer the questions below

What is the first ingredient that Rick needs?
Correct Answer
What is the second ingredient in Rick’s potion?
Correct Answer
What is the last and final ingredient?
Correct Answer
How likely are you to recommend this room to others?

Kaynak <https://tryhackme.com/room/picklerick> 




