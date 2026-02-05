# TryHackMe_Writeups

nmap ile açık port taraması. 22 ssh ve 80 http portlarının açık olduğunu görüyoruz
![1](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/d207f53c-da4d-42b5-815b-33149af200e0)

80 http, web sayfasında işimize yarar bir şey yok, directory taraması yapalım
![2](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/0a704600-ffac-44c6-8e20-c4f3ee7c77df)

admin dizinini bulduk  
![3](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/ced969ce-6867-4770-891f-ef0bcfa2e7f5)  

kodu incelediğimiz zaman bir cookie zaafiyetiyle karşılaşiyoruz, kodda kısaca token varsa cookie olarak kabul et ve admin yetkisi ver anlamını taşıyor  
![5](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/b06664c5-d296-4e5c-ad17-5681f1747a7b)

bu kısımda admin yetkisine sahip cookiyi tanımlıyoruz
![4](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/158a5fc1-08a4-411a-bc75-db9f41a5b3c1)


ve sayfayı yenilediğimizde karşımıza ssh sahibi james ve şifresi
![6](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/aa0d3f6f-9d09-4445-8eca-ef2f1bb77f62)

önce ssh ile bağlanmaya çalışalım  
![7](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/8ad2ecb4-7e9d-4ccd-89b2-3f9ed4e8d28b)

bağlantıda sıkıntı yok ama şifreyi bilmediğimiz için bağlanamıyoruz, bu kısımda john kullanarak şifreyi kıralım.
önce ssh şifresini test.txt metininin içine kaydedelim ardından locate komutu ile ssh2john dosyasının konumunu bulalaım. bulduğumuz konumu yazdıktan sonra text dosyasını kırıp hashes.txt dosyasına çevirmesini belirttik   
![8](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/ecfdfb68-50af-49ac-8c3c-0ab1373be38c)

hashes.txt'ye dönen şifreyi john ile aşşağıdaki komutu kullanarak kırabiliriz. ve şifresi james13  
![9](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/9f016c22-9b0f-4b2a-b4b4-d61e07e780a9)

aşşağıdaki ssh komuyu ile james'e bağlanalım ve şifreyi girelim
![10](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/0148445b-d705-48de-ada1-f10ffb64e084)

giriş yaptıktan sonra masaüstünü kontrol ediyoruz ve karşımıza user adlı bir txt dosyası çıkıyor, bunu cat komutu ile okuyalım ve şifremiz  
![11](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/11b8ceaa-09e4-4e3f-9b5d-1dc0ca95f90b)
