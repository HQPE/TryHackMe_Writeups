<h1>❤️ Love at First Breach 2026 – Hidden Deep Into my Heart Writeup</h1>

<h2>Objective</h2>
Bu makinede hedef, web uygulaması üzerinde keşif yaparak gizli dizinleri bulmak ve zafiyetleri kullanarak admin paneline erişim sağlamaktır.
<br>
<br>

> Web 
---

<h2>Nmap Scan</h2>

Makineye karşı ilk olarak port taraması gerçekleştirildi. Açık port olarak HTTP servisi tespit edildi ve web uygulaması üzerinden devam edildi.

<img width="813" height="323" alt="image" src="https://github.com/user-attachments/assets/fb2e570c-ad35-46af-876a-0cdb06c9c32a" />

<hr>

<h2>Web Uygulamasının İncelenmesi</h2>

Web sitesine erişim sağlandı ve ilk görünüm analiz edildi. Görsel olarak belirgin bir açık tespit edilmediği için dizin brute-force aşamasına geçildi.

<img width="1226" height="856" alt="image" src="https://github.com/user-attachments/assets/f8a537a3-f81a-4c6d-bb21-e7ec629e6c52" />

<hr>

<h2>Gobuster ile Dizin Keşfi</h2>

Gobuster kullanılarak gizli dizin ve dosyalar tarandı. Tarama sonucunda robots.txt dosyası keşfedildi.

<img width="824" height="365" alt="image" src="https://github.com/user-attachments/assets/9d1136ad-c864-412f-945d-3ac5a060fa50" />

<hr>

<h2>robots.txt Analizi</h2>

robots.txt dosyası incelendiğinde gizli bir dizin ve yorum satırı içerisinde parola bilgisi tespit edildi.
Tespit edilen kritik bilgiler:

- /cupids_secret_vault/
- cupid_arrow_2026!!!

Bu durum Information Disclosure (Bilgi Sızması) zafiyetini göstermektedir.

<img width="871" height="560" alt="image" src="https://github.com/user-attachments/assets/378c031f-6f70-49d0-a167-3dcfafa4889d" />


<hr>

<h2>Secret Vault Dizini</h2>

robots.txt içerisinde bulunan dizine erişim sağlandı. Sayfa üzerinde doğrudan içerik bulunmasa da “There’s more to discover...” mesajı ileri keşif gerektiğini gösterdi.

<img width="1023" height="749" alt="image" src="https://github.com/user-attachments/assets/16d7d342-e0f8-4d92-b002-ba492036633f" />

<hr>

<h2>Vault Üzerinde Tekrar Gobuster</h2>

Secret vault dizini üzerinde yeniden brute-force işlemi gerçekleştirildi ve ek endpointler araştırıldı.

<img width="812" height="358" alt="image" src="https://github.com/user-attachments/assets/1a62f14d-e4d5-45b7-bffe-eb3c4b9cf213" />

<hr>

<h2>Admin Panel Keşfi</h2>

Yapılan tarama sonucunda admin giriş ekranı tespit edildi.

<img width="1262" height="838" alt="image" src="https://github.com/user-attachments/assets/f8266749-66ed-4b00-bb72-d246547e9a6a" />

<hr>

<h2>Yetkilendirme ve Giriş Süreci</h2>

robots.txt dosyasında bulunan parola bilgisi (<strong>cupid_arrow_2026!!!</strong>) analiz edildi.

Admin panel üzerinde olası kullanıcı adları test edildi:

- admin  
- administrator  
- root  
- cupid  

Parola olarak robots.txt içerisinde bulunan değer kullanıldı.

Yapılan denemeler sonucunda aşağıdaki kombinasyon ile başarılı giriş sağlandı:

<strong>Kullanıcı Adı:</strong> admin  
<strong>Şifre:</strong> cupid_arrow_2026!!!

<img width="1028" height="731" alt="image" src="https://github.com/user-attachments/assets/66bc76b4-292e-4796-8088-91e921b8a4b1" />

<hr>
