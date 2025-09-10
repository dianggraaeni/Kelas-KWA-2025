# Juice-Shop Write-up: Login Jim

Kategori: SQL Injection  
Tingkat Kesulitan: ⭐⭐⭐ (3/6)  

Deskripsi: Melakukan login ke akun pengguna "Jim" dengan memanfaatkan kerentanan **SQL Injection** pada form login. Email Jim diperoleh melalui ulasan produk (information disclosure), kemudian digunakan untuk eksploitasi SQLi agar bisa login tanpa password.  

## Link Resource
https://demo.owasp-juice.shop/#/

## Jawaban + Bukti
### a. Step-by-step
1. **Analisis Kerentanan**  
   - Sama seperti challenge login admin → kerentanan SQL Injection pada form login.  

2. **Pengumpulan Informasi**  
   - Cari email Jim melalui ulasan produk (customer review).  
   - Ditemukan email: `jim@juice-sh.op`.

<img width="1906" height="957" alt="image" src="https://github.com/user-attachments/assets/f75ab011-63ce-493a-b90c-d3716dd3aaaa" />


3. **Gunakan Payload SQL Injection**  
   - Email: `jim@juice-sh.op'--`  
   - Password: bebas (akan diabaikan).

<img width="1919" height="948" alt="image" src="https://github.com/user-attachments/assets/12f4e37c-e82e-4f15-bc26-d6b21ea468c7" />


4. **Login**  
   - Klik **Login**.  
   - Query SQL terpotong → berhasil login sebagai **Jim**.

<img width="1914" height="953" alt="image" src="https://github.com/user-attachments/assets/91f39a0e-ebdb-4e30-bbab-9cc82e602f9c" />

<img width="1903" height="953" alt="image" src="https://github.com/user-attachments/assets/12889cda-33b4-4392-b9ea-1413d9aaaf43" />



### b. Catatan Hasil Percobaan  

**Status:** Berhasil login sebagai **Jim**  

#### Alasan Keberhasilan
- Aplikasi tidak melakukan sanitasi input.  
- Tidak menggunakan prepared statements.  
- Payload bekerja karena:  
  - `'` → menutup string awal.  
  - `--` → mengabaikan sisa query (password di-skip).  

#### Refleksi
- **SQL Injection** bisa dimanfaatkan untuk login ilegal, mencuri data, bahkan menguasai database.  
- **Pencegahan:**  
  - Gunakan **prepared statements** / ORM aman.  
  - Terapkan validasi & sanitasi input server-side.  

### c. Dokumentasi
- Temuan email Jim melalui review produk.

<img width="1906" height="957" alt="image" src="https://github.com/user-attachments/assets/4faf0726-a47e-4d54-9bdc-3f0182e7c545" />

- Login dengan payload SQLi.
<img width="1919" height="948" alt="image" src="https://github.com/user-attachments/assets/ab0a0199-dca6-4809-b2b4-a935e0754120" />

- Berhasil masuk ke akun Jim.
<img width="1914" height="953" alt="image" src="https://github.com/user-attachments/assets/99ade592-2a94-4795-a01d-a279b758a501" />

<img width="1903" height="953" alt="image" src="https://github.com/user-attachments/assets/ae88b5c4-170f-4cc1-920f-2c99bd29f40f" />

