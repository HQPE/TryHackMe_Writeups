# TryHackMe Writeup

## Port Taraması

İlk olarak hedef makinede açık portları tespit etmek için **nmap** ile tarama yapıyoruz.  
Sonuçlara göre **22 (SSH)** ve **80 (HTTP)** portlarının açık olduğunu görüyoruz.

![Image](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/d207f53c-da4d-42b5-815b-33149af200e0)

---

## Web Servisinin İncelenmesi

80 numaralı port üzerinden web servisine erişiyoruz.  
Ana sayfada doğrudan işimize yarayacak bir bilgi bulunmadığı için dizin taraması yapmaya karar veriyoruz.

![Image](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/0a704600-ffac-44c6-8e20-c4f3ee7c77df)

---

## Dizin Taraması

Yaptığımız tarama sonucunda **admin** dizinini keşfediyoruz.

![Image](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/ced969ce-6867-4770-891f-ef0bcfa2e7f5)

---

## Cookie Zafiyetinin Keşfi

Admin dizinindeki kodları incelediğimizde bir **cookie zafiyeti** ile karşılaşıyoruz.  
Kodun mantığı şu şekilde çalışıyor:

- Eğer tarayıcıda `token` adlı bir cookie varsa
- Kullanıcı otomatik olarak **admin yetkisine** sahip oluyor

![Image](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/b06664c5-d296-4e5c-ad17-5681f1747a7b)

Bu nedenle tarayıcıda admin yetkisine sahip bir cookie tanımlıyoruz.

![Image](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/158a5fc1-08a4-411a-bc75-db9f41a5b3c1)

---

## Admin Bilgilerinin Ele Geçirilmesi

Sayfayı yenilediğimizde karşımıza **SSH kullanıcısı james** ve ona ait bilgiler çıkıyor.

![Image](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/aa0d3f6f-9d09-4445-8eca-ef2f1bb77f62)

---

## SSH Erişimi

Elde ettiğimiz kullanıcı adı ile SSH bağlantısı kurmayı deniyoruz.

![Image](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/8ad2ecb4-7e9d-4ccd-89b2-3f9ed4e8d28b)

Bağlantı başarılı olsa da şifre bilinmediği için giriş yapamıyoruz.

---

## SSH Şifresinin Kırılması

Bu aşamada **John the Ripper** kullanarak SSH anahtarını kırıyoruz.

- SSH anahtarını `test.txt` dosyasına kaydediyoruz
- `ssh2john` aracı ile hash formatına çeviriyoruz
- Oluşan hash’i John ile kırıyoruz

![Image](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/ecfdfb68-50af-49ac-8c3c-0ab1373be38c)

Şifre başarıyla kırılıyor:

**Şifre:** `james13`

![Image](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/9f016c22-9b0f-4b2a-b4b4-d61e07e780a9)

---

## Sisteme Giriş

Elde ettiğimiz bilgilerle tekrar SSH bağlantısı kuruyoruz.

![Image](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/0148445b-d705-48de-ada1-f10ffb64e084)

---
## User Flag

Sisteme giriş yaptıktan sonra masaüstünü kontrol ediyoruz.  
`user.txt` dosyasını okuyarak **user flag**’i elde ediyoruz.
![Image](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/11b8ceaa-09e4-4e3f-9b5d-1dc0ca95f90b)
