# Logbook Proyek PBL Keamanan Siber - Kelompok 04

**Skenario:** Data Integrity Shield  
**Tema:** Design, Attack, and Defend: SOC Analyst Simulation  
**Target Aset:** File Server berbasis Linux (Samba/SMB)  
**Fokus Ancaman:** Ransomware Behavior Simulation (Bulk Encryption)

---

## Anggota Kelompok

1. **Bramantyo Wicaksono** – Lead Analyst  
2. **Ilham Alfianda Fatwa** – System/Network Engineer  
3. **Muhammad Faturrahman** – Security Analyst  

---

## Deskripsi Skenario

Pada skenario *Data Integrity Shield*, kelompok kami merancang dan membangun sebuah infrastruktur **File Server berbasis Linux menggunakan layanan Samba (SMB Protocol)** yang berfungsi sebagai media penyimpanan dokumen internal organisasi.

Simulasi insiden yang dilakukan adalah **Ransomware Behavior Simulation**, yaitu pembuatan skrip yang melakukan *bulk encryption* terhadap file dummy dalam direktori tertentu. Aktivitas ini dirancang untuk meniru pola serangan ransomware nyata yang mengenkripsi banyak file dalam waktu singkat guna mengganggu ketersediaan dan integritas data.

Simulasi dilakukan secara terkontrol dan terbatas hanya pada folder yang telah ditentukan, tanpa menyentuh file sistem operasi, sesuai dengan *Rules of Engagement* yang telah disepakati.

---

## Tujuan Proyek

Tujuan utama dari proyek ini adalah:

- Mengidentifikasi perubahan file dalam jumlah besar sebagai indikator serangan ransomware  
- Menganalisis lonjakan traffic SMB akibat aktivitas enkripsi massal  
- Mengumpulkan dan mengevaluasi log serangan melalui sistem monitoring (Security Onion)  
- Melakukan proses *Incident Response* berdasarkan standar **NIST Incident Response Lifecycle**  

