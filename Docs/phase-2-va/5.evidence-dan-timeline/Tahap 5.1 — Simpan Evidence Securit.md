# 5. Evidence Collection dan Preliminary Analysis

## Tujuan



Tahap ini bertujuan mengumpulkan seluruh artefak hasil simulasi ransomware untuk digunakan pada fase analisis insiden dan penyusunan laporan incident response.

---

### Tahap 5.1 — Evidence Monitoring Security Onion



Selama simulasi serangan, Security Onion digunakan untuk menangkap trafik SMB antara host attacker dan server Samba.



Aktivitas yang berhasil diamati meliputi:



Koneksi SMB dari host attacker 192.168.4.100

Operasi file overwrite

Aktivitas upload file hasil enkripsi

Aktivitas rename massal file menjadi .locked



Contoh log trafik yang tertangkap:


```
IP 192.168.4.100.58260 > 192.168.4.5.microsoft-ds

IP 192.168.4.5.microsoft-ds > 192.168.4.100.58260
```


Hasil ini menunjukkan adanya komunikasi SMB yang intensif selama proses simulasi ransomware berlangsung.

---

### Tahap 5.2 — Evidence dari Host Attacker



Seluruh script yang digunakan selama simulasi disimpan sebagai artefak investigasi.


```
mkdir -p \~/evidence
```

```
cp bulk\_modify.sh \~/evidence/

cp encrypt\_files.sh \~/evidence/

cp rename\_locked.sh \~/evidence/
```


Verifikasi:


```
ls -l \~/evidence
```


Output:



bulk\_modify.sh

encrypt\_files.sh

rename\_locked.sh

Fungsi Script

Script	Fungsi

bulk\_modify.sh	Mengubah isi banyak file secara massal

encrypt\_files.sh	Melakukan simulasi enkripsi menggunakan Base64

rename\_locked.sh	Mengubah nama file menjadi ekstensi .locked

---

### Tahap 5.3 — Evidence File Terenkripsi



Salah satu file hasil simulasi diambil sebagai sampel bukti.



Mengambil file dari SMB share:



smbclient //192.168.4.5/restricted -U user1

get file1.locked evidence\_file1.locked



Verifikasi isi file:



cat evidence\_file1.locked



Output:



RG9rdW1lbiBwZW50aW5nIHBlcnVzYWhhYW4gMQo=



Nilai tersebut merupakan hasil encoding Base64 dari isi file asli.



Contoh decoding:



echo "RG9rdW1lbiBwZW50aW5nIHBlcnVzYWhhYW4gMQo=" | base64 -d



Hasil:



Dokumen penting perusahaan 1



Hal ini menunjukkan bahwa isi file telah berubah menjadi format yang tidak dapat langsung dibaca oleh pengguna.

---

### Tahap 5.4 — Evidence dari Server



Listing direktori server disimpan untuk menunjukkan kondisi akhir file setelah simulasi.



Perintah:



ls -lsh /srv/samba/share/restricted/ > evidence\_listing.txt



Sebagian hasil:



file1.locked

file2.locked

file3.locked

...

file10.locked



Ditemukan pula file kosong dengan nama asli:



file1.txt

file2.txt

...

file10.txt



Kondisi ini muncul karena script enkripsi dijalankan kembali setelah proses rename sehingga file baru dengan nama lama dibuat ulang oleh SMB client.



Meskipun demikian, file .locked tetap menyimpan hasil simulasi enkripsi yang valid dan dapat digunakan sebagai evidence utama.

---

### Tahap 5.5 — Identifikasi IOC (Indicators of Compromise)



Berdasarkan hasil simulasi, beberapa IOC berhasil diidentifikasi.



IOC Jaringan



Source IP



192.168.4.100



Destination IP



192.168.4.5



Protocol



SMB (TCP/445)

IOC File System



Perubahan nama file:



file1.txt → file1.locked

file2.txt → file2.locked

...

file10.txt → file10.locked



Perubahan isi file:



Dokumen penting perusahaan 1



menjadi:



RG9rdW1lbiBwZW50aW5nIHBlcnVzYWhhYW4gMQo=

IOC Aktivitas

Banyak file diakses dalam waktu singkat.

Banyak operasi SMB Write.

Banyak operasi SMB Rename.

Perubahan massal isi file.

Perubahan ekstensi file menjadi .locked.

Tahap 5.6 — Timeline Serangan

Waktu	Aktivitas

T0	Attacker melakukan koneksi SMB ke server

T1	Attacker mengakses folder restricted

T2	Bulk file modification dilakukan

T3	Isi file diganti secara massal

T4	Bulk encryption (Base64) dijalankan

T5	File hasil enkripsi diunggah kembali ke server

T6	Bulk rename menjadi .locked

T7	Security Onion menangkap lonjakan trafik SMB

T8	Evidence dikumpulkan untuk analisis

---

## Kesimpulan



Simulasi ransomware berhasil menunjukkan karakteristik utama serangan ransomware terhadap Samba File Server, yaitu:



Modifikasi massal file melalui SMB.

Perubahan isi file sehingga tidak dapat dibaca secara langsung.

Perubahan nama file menggunakan ekstensi khusus (.locked).

Peningkatan aktivitas SMB yang dapat diamati oleh Security Onion.

Tersedianya artefak forensik berupa file terenkripsi, script serangan, serta log jaringan untuk proses investigasi lebih lanjut.

