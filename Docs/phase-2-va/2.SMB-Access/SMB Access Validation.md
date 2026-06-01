# SMB Access Validation Report



## 1. Objective



Pengujian dilakukan untuk memverifikasi bahwa pengguna SMB yang memiliki kredensial valid dapat mengakses dan melakukan operasi file pada shared folder server. Hasil pengujian ini menjadi dasar untuk tahap simulasi ransomware pada fase berikutnya.



---



\## 2. SMB Login Validation



\### Command



```bash

smbclient //192.168.4.5/restricted -U user1

```



\### Result



Login berhasil dan pengguna memperoleh prompt SMB.



\### Analysis



Kredensial user1 berhasil digunakan untuk mengakses shared folder restricted.



\---



\## 3. File Enumeration Validation



\### Command



```bash

ls

```



\### Result



Berhasil menampilkan daftar file pada shared folder.



\### Analysis



Attacker dapat mengidentifikasi file yang tersedia sebagai target potensial untuk dimodifikasi.



\---



\## 4. File Download Validation



\### Command



```bash

get file9.txt

```



\### Result



File berhasil diunduh ke mesin Kali Linux.



\### Analysis



Pengguna memiliki kemampuan untuk mengambil data dari server sehingga berpotensi memengaruhi kerahasiaan data.



\---



\## 5. File Upload Validation



\### Command



```bash

put test-upload.txt

```



\### Result



File berhasil ditambahkan ke shared folder.



\### Analysis



Pengguna memiliki hak tulis terhadap shared folder sehingga dapat membuat file baru pada server.



\---



\## 6. File Rename Validation



\### Command



```bash

rename test-upload.txt test-encrypted.txt

```



\### Result



Nama file berhasil diubah.



\### Analysis



Pengguna dapat melakukan perubahan terhadap struktur file. Aktivitas ini menyerupai salah satu perilaku ransomware yang mengubah nama atau ekstensi file setelah proses modifikasi.



\---



\## 7. File Deletion Validation



\### Command



```bash

del test-encrypted.txt

```



\### Result



File berhasil dihapus dari shared folder.



\### Analysis



Pengguna memiliki hak untuk menghapus file sehingga dapat menyebabkan kehilangan data apabila disalahgunakan.



\---



\## 8. Server-side Verification



\### Command



```bash

ls -lah /srv/samba/share/restricted/

```



\### Result



Perubahan yang dilakukan melalui SMB terlihat langsung pada filesystem server.



\### Analysis



Aktivitas upload, rename, dan delete yang dilakukan dari Kali Linux berhasil diterapkan pada file yang tersimpan di server.



\---



\## 9. Network Monitoring Verification



\### Command



```bash

sudo tcpdump -i eth1 port 445

```



\### Result



Traffic SMB antara attacker (192.168.4.100) dan server (192.168.4.5) berhasil ditangkap.



\### Analysis



Aktivitas SMB menghasilkan jejak jaringan yang dapat diamati oleh Security Onion dan digunakan sebagai evidence pada proses deteksi dan investigasi insiden.



\---



\## 10. Conclusion



Pengujian menunjukkan bahwa akun user1 memiliki hak yang cukup untuk melakukan operasi file pada shared folder restricted, termasuk membaca, menulis, mengubah nama, dan menghapus file. Kemampuan ini memungkinkan simulasi perilaku ransomware dilakukan pada fase berikutnya dengan tetap berada dalam ruang lingkup skenario internal compromised endpoint.



