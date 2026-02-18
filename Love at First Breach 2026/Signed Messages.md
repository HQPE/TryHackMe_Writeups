# ❤️ Love at First Breach 2026 – Signed Messages Writeup

## Objective

**My Dearest Hacker,**

Bu writeup’un amacı, LoveNote web uygulamasındaki **deterministik RSA zafiyetini keşfetmek ve exploit etmek**. Sistem her mesajı imzaladığını iddia etse de debug logları, private key’in tahmin edilebilir olduğunu gösteriyor. Biz bu zafiyeti kullanarak admin imzasını taklit ettik ve güvenlik açığını doğruladık.


> Web | Crypto
---

## Nmap Scan

İlk adım olarak makineye port taraması gerçekleştirildi. Açık servisleri belirlemek ve olası hedefleri keşfetmek için Nmap kullanıldı.  

Tarama sonucunda **HTTP servisi** açık olarak tespit edildi ve web uygulaması üzerinden keşif işlemine devam edildi.  

![Nmap Scan](https://github.com/user-attachments/assets/c3c222c6-4726-48e6-905f-e52c60270f14)

---

## Web Application Analysis

Web sitesine erişim sağlandı ve ilk görünüm analiz edildi.  

- Kullanıcı arayüzü incelendi  
- Açık dizin veya dosya linkleri gözle kontrol edildi  
- İlk analizde belirgin bir açık tespit edilmedi  

Bu nedenle, gizli dizinleri bulmak için keşif aşamasına geçildi.  


![Web Screen](https://github.com/user-attachments/assets/6f6da2ed-5679-4649-bc5c-4f9d0245ed04)

---

## Gobuster Directory Enumeration

Dizin keşfi için Gobuster kullanıldı.  

- Gizli dizinler ve dosyalar tarandı  
- Tarama sonucunda **debug uzantılı dizin** keşfedildi  


![Gobuster Screen](https://github.com/user-attachments/assets/52eed510-57cb-42ab-838e-6e0bd3ad4c60)

---

## Debug Directory Analysis

`/debugh` dizini incelendiğinde, sistemin **deterministic RSA key generation** kullandığı ortaya çıktı.  

- Seed pattern: `{username}_lovenote_2026_valentine`  
- Prime türetme işlemleri ve RSA modülü loglanmış  
- Debug logları, private key’in tekrar üretilebileceğini açıkça gösteriyor  


![Debug Output](https://github.com/user-attachments/assets/298b7a53-bafb-4deb-8d92-1ed7b523acf8)

---

## User Creation & Key Extraction

Sistemde yeni bir kullanıcı oluşturulduktan sonra **public key** ve **private key** bilgilerine erişim sağlandı.  

- Anahtarların deterministik olarak üretildiği doğrulandı  
- Private key’in tahmin edilebilir olduğu görüldü  


![Public & Private Key](https://github.com/user-attachments/assets/6f1cf1ee-bd01-446b-a214-5905a5407e02)

---

## Exploit Development

Elde edilen seed bilgisi kullanılarak admin için **aynı RSA anahtarı** yeniden üretildi.  

- Python ve PyCryptodome kullanıldı  
- Deterministik seed ve prime türetme yöntemleri tekrar uygulandı  
- Mesaj imzalandı  

```python
import hashlib
from sympy import nextprime
from Crypto.PublicKey import RSA
from Crypto.Signature import pss
from Crypto.Hash import SHA256
from Crypto.Util.number import inverse

TARGET_USER = "admin"
TARGET_MESSAGE = "Hello"

def generate_signature():
    seed = f"{TARGET_USER}_lovenote_2026_valentine".encode()

    p = nextprime(int(hashlib.sha256(seed).hexdigest(), 16))
    q = nextprime(int(hashlib.sha256(seed + b"pki").hexdigest(), 16))

    n = p * q
    e = 65537
    phi = (p - 1) * (q - 1)
    d = inverse(e, phi)

    key = RSA.construct((n, e, d, p, q))
    h = SHA256.new(TARGET_MESSAGE.encode())

    signer = pss.new(key)
    signature = signer.sign(h)

    return signature.hex()

print(generate_signature())

```
sonuç
<img width="1538" height="735" alt="image" src="https://github.com/user-attachments/assets/1973edd5-5282-4bcc-9ea9-bf54bba4d562" />


