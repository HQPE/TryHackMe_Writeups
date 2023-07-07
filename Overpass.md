# TryHackMe_Writeups

nmap ile açık port taraması. 22 ssh ve 80 http portlarının açık olduğunu görüyoruz
![1](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/d207f53c-da4d-42b5-815b-33149af200e0)

80 http, web sayfasında işimize yarar bir şey yok, directory taraması yapalım
![2](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/0a704600-ffac-44c6-8e20-c4f3ee7c77df)

/admin dizinini elde ettik. klasik şifre kombinasyonu deneyebiliriz ya da cookieler üzerinden hareket edebiliriz
![3](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/d5232b8d-5583-4f6b-87dd-e759a68c6db2)

kodu incelediğimiz zaman bir cookie zaafiyetiyle karşılaşiyoruz, kodda kısaca token varsa cookie olarak kabul et ve admin yetkisi ver anlamını taşıyor
![4](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/158a5fc1-08a4-411a-bc75-db9f41a5b3c1)


![5](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/b06664c5-d296-4e5c-ad17-5681f1747a7b)
![6](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/aa0d3f6f-9d09-4445-8eca-ef2f1bb77f62)
![7](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/8ad2ecb4-7e9d-4ccd-89b2-3f9ed4e8d28b)
![8](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/ecfdfb68-50af-49ac-8c3c-0ab1373be38c)
![9](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/9f016c22-9b0f-4b2a-b4b4-d61e07e780a9)
![10](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/0148445b-d705-48de-ada1-f10ffb64e084)
![11](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/11b8ceaa-09e4-4e3f-9b5d-1dc0ca95f90b)
![12](https://github.com/HQPE/TryHackMe_Writeups/assets/65927735/3e9baedb-3449-4e2b-abbe-1f95323db169)
