# Juice-Shop Write-up: Users Credentials

Kategori: Injection  
Tingkat Kesulitan: ⭐⭐⭐⭐ (4/6)  

Deskripsi: Challenge ini mengeksploitasi SQL Injection pada endpoint pencarian produk untuk mengekstrak kredensial pengguna (email, username, password, role, dll.) langsung dari database.

## Link Resource
https://demo.owasp-juice.shop/#/

## Jawaban + Bukti
### a. Step-by-step
1. **Identifikasi Titik Injeksi**
   - Menggunakan Burp Suite ditemukan endpoint: `/rest/products/search?q=apple`.
   - Input `'` memicu error → indikasi rentan SQLi.

2. **Uji Payload**
   - Percobaan awal dengan `' OR 1=1--` menghasilkan error kolom mismatch.
   - Dari error, disimpulkan query membutuhkan jumlah kolom yang sesuai.

3. **Payload UNION SELECT**
   - Setelah beberapa percobaan, payload disusun agar sesuai struktur tabel Products.

   Payload akhir:
```
none' UNION SELECT null,id,null,null,email,password,role,deletedAt,isActive,username,null,createdAt FROM Users--
```

4. **Hasil**
- Query berhasil menampilkan data sensitif: email, password, username, role, dll.
- Challenge selesai.

### b. Catatan Hasil Percobaan
**Status:** Berhasil mengekstrak data kredensial pengguna.  

#### Alasan Keberhasilan
- Endpoint `/rest/products/search` tidak memvalidasi input.  
- Aplikasi tidak menggunakan prepared statements/ORM.  
- Error server (kolom mismatch) memberi petunjuk jumlah kolom.  
- Payload UNION SELECT menggabungkan data tabel Users ke output Products.  

#### Refleksi
- SQL Injection dapat mengakibatkan kebocoran data sensitif dan pengambilalihan database.  
- Input pengguna harus divalidasi & disanitasi.  
- Gunakan **prepared statements** atau ORM.  
- Terapkan **least privilege** untuk akun database aplikasi.  

### c. Dokumentasi
1. **Identifikasi Titik Injeksi**  
Tampilan Burp Suite Proxy HTTP history, menunjukkan permintaan GET `/rest/products/search?q=apple` dan respons JSON produk. Ini adalah langkah awal identifikasi titik injeksi.  
<img width="1919" height="1007" alt="image" src="https://github.com/user-attachments/assets/033bf606-26ca-47aa-89be-74d40ee6a702" />

2. **Uji Payload Awal (`'`)**  
Tampilan browser menunjukkan hasil dari pengujian SQL Injection dengan memasukkan tanda kutip tunggal (`'`) pada parameter `q`. Aplikasi merespons dengan status sukses tetapi mengembalikan data kosong.  
<img width="1919" height="952" alt="image" src="https://github.com/user-attachments/assets/202c3e19-e40c-4d65-b519-8b856f1a9745" />

3. **Uji Payload Awal (kosong)**  
Tampilan Burp Suite Repeater yang menunjukkan permintaan GET `/rest/products/search?q=` dan respons HTTP 200 OK yang sukses dengan data produk berformat JSON.  
<img width="1919" height="1008" alt="image" src="https://github.com/user-attachments/assets/c436ece5-9223-4532-9d11-191582358bb9" />

4. **Uji Payload `' OR 1=1--` (Error 400)**  
Tampilan Burp Suite Repeater menunjukkan permintaan GET `/rest/products/search?q=' OR 1=1--` yang mendapat respons HTTP 400 Bad Request.  
<img width="1919" height="1000" alt="image" src="https://github.com/user-attachments/assets/c31925fb-2594-4e80-ac8f-9dc24feb5934" />

5. **Error 500 (incomplete input)**  
Tampilan halaman kesalahan HTTP 500 pada OWASP Juice Shop yang menunjukkan pesan `SQLITE_ERROR: incomplete input`. Ini adalah hasil dari upaya SQL Injection, membuktikan bahwa parameter pencarian (`q`) pada URL rentan terhadap serangan.  
<img width="1915" height="658" alt="image" src="https://github.com/user-attachments/assets/84ba6c16-a9b5-4f15-b0bd-83e7db07e82e" />

6. **Error 500 (detail query gagal)**  
Tampilan Burp Suite Repeater menunjukkan permintaan GET `/rest/products/search?q=' OR 1=1--` yang mendapat respons HTTP 500 Internal Server Error. Respons ini juga menyertakan pesan error `SQLITE_ERROR: incomplete input` dan kueri SQL yang gagal.  
<img width="1919" height="998" alt="image" src="https://github.com/user-attachments/assets/e1d59083-fb8a-48cf-9de3-9655d6aef23c" />

7. **Payload UNION SELECT Berhasil**  
Tampilan browser menunjukkan keberhasilan serangan SQL Injection. Dengan menggunakan payload `UNION SELECT`, penyerang berhasil mengekstrak dan menampilkan data sensitif dari database, termasuk email, password, dan role pengguna.  
<img width="1918" height="278" alt="image" src="https://github.com/user-attachments/assets/9ca3ec6f-8839-4feb-a59f-bccb898a58c4" />

8. **Tampilan berhasil menyelesaikan challenge**  
<img width="721" height="358" alt="image" src="https://github.com/user-attachments/assets/698412c5-f9e2-48ac-ba57-766f43dac6b9" />  

<img width="1919" height="956" alt="image" src="https://github.com/user-attachments/assets/0af55c09-04ec-4890-801b-21e2399db23f" />
