# Juice-Shop Write-up: Login Bender Challenge

Kategori: SQL Injection  
Tingkat Kesulitan: ⭐⭐⭐ (3/6)  

Deskripsi: Melakukan login ke akun pengguna "Bender" dengan memanfaatkan kerentanan SQL Injection pada form login untuk melewati autentikasi tanpa mengetahui password asli.

## Link Resource
https://demo.owasp-juice.shop/#/

## Jawaban + Bukti
### a. Step-by-step
1. **Identifikasi Email Bender**
   - Login sebagai admin untuk mengakses panel administrasi.
   - Ditemukan email milik Bender: `bender@juice-sh.op`.
     
<img width="1913" height="959" alt="image" src="https://github.com/user-attachments/assets/ece6721f-6cff-456d-9301-94b056fe0389" />


2. **Percobaan Login sebagai Bender**
   - Buka halaman login aplikasi.
   - Masukkan email Bender pada kolom **Email**.
  
    

3. **Gunakan Payload SQL Injection**
   - Email: `bender@juice-sh.op' AND '1'='1' --`
   - Password: bebas (diabaikan karena komentar `--`).

<img width="540" height="677" alt="image" src="https://github.com/user-attachments/assets/1cafdb97-fcc1-453d-ac21-53089390d211" />


4. **Login**
   - Klik **Login**.
   - Query menjadi selalu bernilai **true** → berhasil login sebagai **Bender**.  

<img width="1911" height="943" alt="image" src="https://github.com/user-attachments/assets/830e9f67-ebc5-4392-b9a7-ee4d8a9da64e" />


### b. Catatan Hasil Percobaan

**Status:** Berhasil login sebagai **Bender**

#### Alasan Keberhasilan
- Aplikasi tidak melakukan sanitasi input pada kolom email.  
- Tidak menggunakan prepared statements, sehingga input `bender@juice-sh.op' AND '1'='1' --` langsung dieksekusi database.  
- Payload bekerja karena:
  - `'` → menutup string awal.
  - `AND '1'='1'` → kondisi selalu benar.
  - `--` → mengabaikan sisa query (termasuk verifikasi password).  
- Autentikasi dianggap valid meskipun password salah.

#### Refleksi
- **SQL Injection** dapat memungkinkan penyerang masuk tanpa password, mencuri data, atau bahkan menguasai database.  
- **Pencegahan:**  
  - Gunakan **prepared statements** / ORM yang aman.  
  - Terapkan validasi & sanitasi input di sisi server.  

### c. Dokumentasi
- Email Bender dari Admin Panel:  
<img width="1913" height="959" alt="image" src="https://github.com/user-attachments/assets/ece6721f-6cff-456d-9301-94b056fe0389" />

- Login sebagai Bender dengan payload SQLi:  

<img width="540" height="677" alt="image" src="https://github.com/user-attachments/assets/1cafdb97-fcc1-453d-ac21-53089390d211" />

<img width="1914" height="958" alt="image" src="https://github.com/user-attachments/assets/22605c8b-4970-4ef1-ba2c-cb29888829c5" />









