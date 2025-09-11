# Juice-Shop Write-up: NoSQL Manipulation

Kategori: Injection  
Tingkat Kesulitan: ⭐⭐⭐ (3/6)  

Deskripsi: Mengeksploitasi kerentanan **NoSQL Injection** pada fitur **update review** sehingga semua review pada produk berubah menjadi pesan manipulasi.

## Link Resource
https://demo.owasp-juice.shop/#/

## Jawaban + Bukti
### a. Step-by-step
1. **Identifikasi Endpoint**  
   - Endpoint menerima request `PUT` untuk update review.  
   - Payload normal hanya mengupdate satu review tertentu.  

2. **Eksperimen Update Review Tunggal**  
   - Dicoba payload biasa:  
     ```json
     {
       "message": "yey kena hackk",
       "author": "Anonymous"
     }
     ```  
   - Hasilnya hanya mengubah **satu review** pada produk `24`.  

<img width="1919" height="1004" alt="image" src="https://github.com/user-attachments/assets/8e5f2520-7310-40f1-8df9-242487584d7a" />

3. **Uji NoSQL Injection**  
   - Ditambahkan field `"id": { "$ne": null }` agar query memilih semua dokumen review.  
   - Payload:  
     ```json
     {
       "id": { "$ne": null },
       "message": "ALL REVIEWS HACKED :)"
     }
     ```  

4. **Payload Final & Perubahan Massal**  
   - Request dikirim lewat Burp Repeater:
   - Hasil: Semua review dari produk berubah.

<img width="1912" height="1012" alt="image" src="https://github.com/user-attachments/assets/95969be4-067b-4be7-9318-c3634b0d76af" />


### b. Catatan Hasil Percobaan

**Status:** Berhasil memanipulasi seluruh review.  

#### Alasan Keberhasilan
- Query MongoDB rentan karena tidak memvalidasi input JSON.  
- Operator `$ne` (not equal) membuat query mencakup semua dokumen.  
- Tidak ada sanitasi/validasi pada field `id` → injeksi berhasil.  

#### Refleksi
- **NoSQL Injection** sama berbahayanya dengan SQL Injection → dapat mengubah/membaca seluruh data.  
- **Pencegahan:**  
  - Terapkan validasi ketat pada input user.  
  - Gunakan whitelist field yang boleh diupdate.  
  - Batasi hak akses database agar tidak semua operasi bisa dilakukan user.  

### c. Dokumentasi
- Uji update review tunggal.
<img width="1919" height="1004" alt="image" src="https://github.com/user-attachments/assets/8e5f2520-7310-40f1-8df9-242487584d7a" />

- Payload dengan `$ne` memanipulasi semua review.
<img width="1912" height="1012" alt="image" src="https://github.com/user-attachments/assets/95969be4-067b-4be7-9318-c3634b0d76af" />


