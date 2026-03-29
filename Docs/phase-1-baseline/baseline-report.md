## baseline-report.md
**1. Deskripsi Topologi**

Sistem terdiri dari tiga virtual machine dalam satu jaringan:

- Attacker: Kali Linux (192.168.4.100)
- Target: Ubuntu Server (192.168.4.10)
- Monitoring: Security Onion (192.168.4.20)

Seluruh VM berada pada jaringan internal 192.168.4.0/24 sehingga memungkinkan simulasi serangan dan monitoring secara real-time.

---

**2. Konfigurasi Identitas Sistem**

| Host     | IP Address    | Hostname       |
| -------- | ------------- | -------------- |
| Kali     | 192.168.4.100 | attacker-04    |
| Ubuntu   | 192.168.4.10  | SRV-WEB-KEL01F |
| SecOnion | 192.168.4.20  | seconion       |

---

**3. Network Hardening**

Dilakukan konfigurasi firewall pada Ubuntu Server menggunakan UFW:

sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw enable

Tujuan:

- Membatasi akses masuk
- Hanya membuka port yang diperlukan (SSH)

**4. System Hardening**

Langkah yang dilakukan:

- Update sistem:
sudo apt update && sudo apt upgrade -y
- Menonaktifkan service tidak perlu:
sudo systemctl disable apache2
- Penggunaan user non-root:
adduser user1
usermod -aG sudo user1

---

**5. Logging & Monitoring (Security Onion)**

Security Onion digunakan untuk monitoring trafik jaringan.

Hasil:

- Service berjalan normal (so-status)
- Snort dan Sguil aktif
- Traffic ICMP berhasil terdeteksi

---

**6. Bukti Logging**

Dilakukan pengujian dengan ping dari attacker ke server:

ping 192.168.4.10

Hasil:

- Traffic ICMP berhasil muncul di dashboard Sguil
- Terdapat informasi:
  - Timestamp
  - Source IP: 192.168.4.100
  - Destination IP: 192.168.4.10

---

**7. Kesimpulan Baseline**

Sistem telah memenuhi kondisi baseline:

- Infrastruktur berjalan
- Hardening dasar telah diterapkan
- Monitoring aktif dan mampu mendeteksi traffic
