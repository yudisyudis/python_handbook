# YUDIS' PYTHON HANDBOOK

# Buku Saku Python Level 1: Fondasi Dasar untuk Data Engineer

## 📘 BAB 1: Sintaks Dasar & Tipe Data
Python akan mendeteksi tipe data secara otomatis. Namun, saat menerima data dari luar (seperti file CSV atau API), data sering kali terbaca sebagai teks (String). Anda harus tahu cara mendeteksi dan mengubahnya (Type Casting).

### 1. Tipe Data Inti
- Integer (Bilangan Bulat) -> Cocok untuk ID, jumlah barang: `user_id = 101`
- Float (Desimal) -> Cocok untuk uang, persentase: `revenue = 500.50`
- String (Teks) -> Cocok untuk nama, status, kota: `status = 'Completed'`
- Boolean (True/False) -> Cocok untuk flag atau penanda: `is_active = True`

### 2. Type Casting (Mengubah Tipe Data)
Data dari log atau CSV biasanya berwujud teks. Anda tidak bisa menjumlahkan teks, jadi harus diubah dulu.
```python
raw_revenue = "1500" 
tax_rate = "0.11"

# Mengubah teks menjadi angka
clean_revenue = int(raw_revenue)       # Hasil: 1500
clean_tax = float(tax_rate)            # Hasil: 0.11

# Jika butuh menggabungkan angka ke dalam teks, ubah kembali ke string
print("Total revenue: " + str(clean_revenue))
