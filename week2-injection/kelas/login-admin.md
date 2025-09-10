# Juice-Shop Write-up: Login Admin

Kategori: SQL Injection

Tingkat Kesulitan: ⭐⭐ (2/6)

Deskripsi: Log in dengan akun administrator pengguna. Challenge ini bertujuan untuk mengeksploitasi kerentanan SQL Injection pada formulir login untuk mendapatkan akses ke akun admin.

## Link Resource
https://demo.owasp-juice.shop/#/

## Jawaban + Bukti
### a. Step-by-step
1. **Akses Halaman Login**
   - Klik tombol **Account > Login**.

2. **Provokasi Error**
   - Masukkan `'` pada kolom **Email** dan value bebas pada kolom **Password**.
   - Klik **Login** → muncul pesan error (indikasi SQL Injection).

3. **Gunakan Payload**
   - Email: `' OR '1'='1' --`
   - Password: bebas (diabaikan karena komentar `--`).

4. **Login**
   - Klik **Login**.
   - Query selalu bernilai **true** → berhasil login sebagai **Administrator**.
  
### b. Catatan Hasil Percobaan

**Status:** Berhasil login sebagai **Admin**

#### Alasan Keberhasilan
- Aplikasi **tidak melakukan sanitasi input** pada kolom email.
- **Tidak menggunakan prepared statements**, sehingga input `' OR '1'='1' --` langsung dieksekusi oleh database.
- Payload bekerja karena:
  - `'` → menutup string awal.
  - `OR '1'='1'` → kondisi selalu benar.
  - `--` → mengabaikan sisa query (password diabaikan).
- Database mengembalikan entri pertama (biasanya admin) → login sukses.

#### Refleksi
- **SQL Injection** termasuk kerentanan web paling berbahaya.
- Dampak potensial:
  - Akses ilegal ke data sensitif.
  - Pencurian atau modifikasi data.
  - Penghapusan database.
- **Pencegahan:**
  - Gunakan **prepared statements** / ORM yang aman.
  - Terapkan validasi & sanitasi input pada sisi server.

### c. Dokumentasi
Challenge Injection - Login Admin

<img width="584" height="209" alt="image" src="https://github.com/user-attachments/assets/ec25afe9-2e89-4e9a-b2d4-df74885164e6" />

Halaman Login Awal dan Provokasi Error:

<img width="291" height="166" alt="image" src="https://github.com/user-attachments/assets/3065da39-7f06-4f45-859a-74e54154abcb" />


<img width="491" height="646" alt="image" src="https://github.com/user-attachments/assets/8cc173c4-d5e2-49cd-926c-4f54ea9595be" />


<img width="481" height="645" alt="image" src="https://github.com/user-attachments/assets/fc4feb33-6089-4eb6-acd0-1d673ea49a51" />


<img width="1919" height="947" alt="image" src="https://github.com/user-attachments/assets/3891b631-2ef9-4642-9c20-8af65f87ad9b" />


Halaman Login dengan Payload SQL Injection

<img width="1918" height="946" alt="image" src="https://github.com/user-attachments/assets/93052413-044f-4f60-8dd6-fd8c5fe5debd" />


Berhasil Login sebagai Admin:
<img width="881" height="95" alt="image" src="https://github.com/user-attachments/assets/e928cc49-de58-475e-9de1-6685efa00f39" />


<img width="313" height="348" alt="image" src="https://github.com/user-attachments/assets/265f33bf-fedf-4306-9670-47cb7bcde52c" />

