# Bounty Hacker - Write-up

* **Platform:** TryHackMe
* **Zorluk Seviyesi:** Easy
* **Makale Amacı:** Bu makinede Nmap ile servis keşfi, FTP anonim girişi üzerinden hassas dosya okuma, Hydra ile SSH kaba kuvvet (brute-force) saldırısı ve GTFOBins ile root yetkisi yükseltme adımları gerçekleştirilmiştir.

---

## 1. Keşif (Reconnaissance)

Hedef IP adresine yönelik gerçekleştirilen kapsamlı Nmap taraması ile açık portlar ve servis sürümleri tespit edilmiştir.

* **Komut:** `nmap -sC -sV -T4 10.114.170.5`
* **Açık Portlar:**
  * **Port 21 (FTP):** vsftpd 3.0.5 (Anonim giriş aktif)
  * **Port 22 (SSH):** OpenSSH 8.2p1
  * **Port 80 (HTTP):** Apache httpd 2.4.41

<img width="1479" height="943" alt="1" src="https://github.com/user-attachments/assets/b92afa16-6e1b-489f-b2e2-f0d206e5a216" />

---

## 2. Servis Analizi & Bilgi Toplama (Enumeration)

* FTP servisine `anonymous` kullanıcı adı ile giriş yapılarak sunucudaki dosyalar listelenmiştir.
* Dizin listesinde bulunan `locks.txt` ve `task.txt` dosyaları `get` komutu ile saldırgan makineye indirilmiştir.
* İndirilen dosyalar incelendiğinde `locks.txt` içerisinde olası şifreler, `task.txt` içerisinde ise kullanıcı adına dair ipuçları (`lin`) yer aldığı görülmüştür.

| Dosya İşlemleri | Açıklama |
| :--- | :--- |
|<img width="1900" height="961" alt="2" src="https://github.com/user-attachments/assets/b8319496-21da-4156-bfe9-6b5260bd456e" /> | FTP üzerinden dosyaların çekilmesi |
|<img width="761" height="854" alt="3" src="https://github.com/user-attachments/assets/f194963e-7adf-4138-a61b-12f113931463" /> | Şifre listesi (`locks.txt`) |
|<img width="907" height="231" alt="4" src="https://github.com/user-attachments/assets/3e962862-51dc-4ca1-9f4d-d5bd021e8f5a" /> | Görev listesi ve ipucu (`task.txt`) |

---

## 3. Sömürü (Exploitation / Foothold)

* `locks.txt` wordlist'i ve tespit edilen `lin` kullanıcı adı kullanılarak SSH servisine Hydra ile kaba kuvvet (brute-force) saldırısı gerçekleştirilmiştir.
* Yapılan tarama sonucunda doğru şifre (`RedDr4gonSynd1cat3`) başarıyla bulunmuştur.
* Elde edilen kimlik bilgileriyle SSH üzerinden sisteme bağlanılmış ve kullanıcı bayrağı (`user.txt`) okunmuştur.

| Sömürü Adımları | Açıklama |
| :--- | :--- |
|<img width="1914" height="601" alt="5" src="https://github.com/user-attachments/assets/abb18938-8079-49f5-a97c-2cf54b886ae2" /> | SSH brute-force saldırısı |
|<img width="1337" height="846" alt="6" src="https://github.com/user-attachments/assets/6d8c4406-1dc6-4a7d-81cf-5a52fe26b25b" /> | Sisteme SSH ile giriş |
|<img width="883" height="130" alt="7" src="https://github.com/user-attachments/assets/bccef494-d916-4565-ae38-22b3e8fb2229" /> | Kullanıcı bayrağının okunması |

---

## 4. Yetki Yükseltme (Privilege Escalation)

* `sudo -l` komutu ile kullanıcının yetkileri denetlendiğinde, `lin` kullanıcısının şifre girerek `/bin/tar` komutunu root yetkileriyle çalıştırabileceği tespit edilmiştir.
* GTFOBins üzerinden `tar` binary'si için yetki yükseltme komutu incelenmiştir.
* Belirtilen komut çalıştırılarak sistemde doğrudan root erişimi sağlanmış ve `root.txt` dosyasına ulaşılmıştır.

| Yetki Yükseltme Adımları | Açıklama |
| :--- | :--- |
|<img width="1658" height="380" alt="8" src="https://github.com/user-attachments/assets/905f5356-4538-4e84-b8a9-58b72793f1dc" /> | Yetkilerin kontrol edilmesi (`tar`) |
|<img width="1019" height="505" alt="9" src="https://github.com/user-attachments/assets/0feca5b8-c91b-428e-914f-c0a0f3f168e4" /> | GTFOBins üzerinden komut tespiti |
|<img width="1907" height="215" alt="10" src="https://github.com/user-attachments/assets/f777bc5a-5c85-4f65-bf7c-f6a0e63df923" /> | Root yetkisi ve bayrağa erişim |
