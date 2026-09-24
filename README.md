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
```

---

## 🚦 BAB 2: Control Flow (Alur Logika)
Digunakan untuk memfilter data, mirip seperti klausa `WHERE` atau `CASE WHEN` di SQL. Kita menggunakan `if`, `elif` (else if), dan `else`.

### 1. Operator Logika Dasar
- Sama dengan: `==` | Tidak sama dengan: `!=`
- Lebih besar / kecil: `>`, `<`, `>=`, `<=`
- Penggabungan kondisi: `and`, `or`, `not`

### 2. Contoh Penggunaan (Validasi Transaksi)

```python
status = "Cancelled"
revenue = 0

if status == "Completed" and revenue > 0:
    print("Transaksi valid, masukkan ke database.")
elif status == "Completed" and revenue == 0:
    print("Transaksi mencurigakan, revenue tidak boleh nol.")
else:
    print("Abaikan, transaksi dibatalkan atau error.")
```

---

## 🔄 BAB 3: Looping (Perulangan)
Di SQL, Anda memproses data secara serentak. Di Python, Anda memproses data baris demi baris (row by row).

### 1. for Loop (Perulangan Terukur)
Paling sering digunakan untuk membaca sekumpulan data.

```python
data_transaksi = [100, 250, 500]
total = 0

for nilai in data_transaksi:
    total = total + nilai
print(total) # Output: 850
```

### 2. Pengendali Loop: break, continue, pass
- `continue`: Lewati baris ini, lanjut ke baris berikutnya (sering dipakai men-skip data kotor).
- `break`: Hentikan looping sepenuhnya sekarang juga.
- `pass`: Tidak melakukan apa-apa (hanya sebagai placeholder).

```python
raw_data = ["100", "error", "200", "STOP", "300"]

for row in raw_data:
    if row == "error":
        continue  # Skip data rusak ini
    if row == "STOP":
        break     # Hentikan semua proses
    print("Memproses angka:", int(row))
```

---

## ✂️ BAB 4: String Manipulation (Manipulasi Teks)
Ini adalah nyawa dari proses ekstraksi teks (ETL). Data dari sistem lawas sering kali berantakan.

### 1. Pembersihan Dasar

```python
raw_city = "   JaKaRTa   \n"
clean_city = raw_city.strip().lower() 
print(clean_city) # Output: "jakarta"
```

### 2. Memecah dan Menggabungkan
Fungsi `.split()` adalah cara paling dasar untuk membuat kolom dari teks.

```python
log_line = "101,Completed,500"
kolom = log_line.split(',') # Hasil: ['101', 'Completed', '500']
status_transaksi = kolom[1] # Hasil: Completed

# .replace() untuk mengganti teks
harga_kotor = "Rp500.000"
harga_bersih = harga_kotor.replace("Rp", "").replace(".", "")
```

### 3. F-Strings (Penyusunan Teks Dinamis)
Cara modern menyisipkan variabel ke dalam teks (untuk generate log atau query dinamis).

```python
hotel_name = "Agoda Resort"
revenue = 15000
pesan = f"Hotel {hotel_name} mendapatkan total revenue sebesar ${revenue}."
```
