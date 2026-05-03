# Proyek PBL Keamanan Siber - Kelompok 04

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

Pada skenario Data Integrity Shield, sistem yang dibangun berupa infrastruktur File Server berbasis Linux menggunakan layanan Samba (SMB) yang berfungsi sebagai media penyimpanan dokumen internal organisasi.

Skenario ini mensimulasikan kondisi insider threat, dimana sebuah endpoint dalam jaringan internal diasumsikan telah terkompromi dan digunakan sebagai titik awal serangan. Oleh karena itu, layanan SMB tetap dapat diakses dari jaringan internal untuk merepresentasikan kondisi nyata operasional sistem.

Simulasi insiden yang dilakukan adalah Ransomware Behavior Simulation, yaitu pembuatan skrip yang melakukan perubahan massal (bulk modification/encryption) terhadap file dummy pada direktori tertentu. Aktivitas ini dirancang untuk meniru karakteristik utama serangan ransomware, yaitu perubahan file dalam jumlah besar dalam waktu singkat yang berdampak pada integritas dan ketersediaan data.

Simulasi dilakukan secara terkontrol dan terbatas hanya pada folder yang telah ditentukan (restricted), tanpa menyentuh file sistem operasi, sesuai dengan Rules of Engagement yang telah disepakati.

---

## Tujuan Proyek

Tujuan utama dari proyek ini adalah:

- Mengidentifikasi perubahan file dalam jumlah besar sebagai indikator serangan ransomware  
- Menganalisis lonjakan traffic SMB akibat aktivitas enkripsi massal  
- Mengumpulkan dan mengevaluasi log serangan melalui sistem monitoring (Security Onion)  
- Melakukan proses *Incident Response* berdasarkan standar **NIST Incident Response Lifecycle**  

