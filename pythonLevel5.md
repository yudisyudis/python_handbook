# Buku Saku Python Level 5: Pythonic & Efisiensi Memori (Pro Level)

Kode yang "jalan" saja tidak cukup jika ia memakan RAM hingga *crash* saat memproses file 10 GB. Level ini berfokus pada cara menulis kode yang lebih singkat (elegan) dan sangat hemat memori.

---

## ✨ BAB 1: Comprehensions (Looping Satu Baris)
*Comprehension* adalah cara khas Python (*Pythonic*) untuk membuat List atau Dictionary baru dari data yang sudah ada, hanya dalam satu baris. Ini lebih cepat diproses oleh mesin daripada menggunakan `for` dan `.append()`.

### 1. List Comprehension
Format: `[ <HASIL_YANG_DIMAU> for <VARIABEL> in <DATA> if <KONDISI> ]`

```python
# CARA LAMA (Pemula):
revenues = [100, 250, -50, 400, 0]
valid_revenues = []
for rev in revenues:
    if rev > 0:
        valid_revenues.append(rev * 1.1)  # Tambah pajak 10%

# CARA PYTHONIC (Pro):
# "Kalikan 1.1 untuk setiap rev di dalam revenues HANYA JIKA rev > 0"
valid_revenues_pro = [rev * 1.1 for rev in revenues if rev > 0]
print(valid_revenues_pro) # Output: [110.0, 275.0, 440.0]
```

### 2. Dictionary Comprehension
Sangat berguna untuk menukar posisi Key dan Value, atau memformat data API.
```python
data_kotor = {"HTL-01": "bali", "HTL-02": "tokyo"}

# Format: { <KEY_BARU>: <VALUE_BARU> for <KEY>, <VALUE> in <DICT>.items() }
data_bersih = {k: v.capitalize() for k, v in data_kotor.items()}
print(data_bersih) # Output: {'HTL-01': 'Bali', 'HTL-02': 'Tokyo'}
```

---

## 🧠 BAB 2: Generators & `yield` (Penyelamat RAM)
Bayangkan Anda harus memproses log transaksi berukuran 50 GB. Jika Anda menyimpannya ke dalam sebuah *List*, RAM komputer Anda (yang mungkin cuma 16 GB) akan langsung meledak (*Out of Memory*).

Solusinya: **Generator**. 
Alih-alih memberikan seluruh data sekaligus dengan `return`, kita memuntahkan data **satu per satu** menggunakan kata kunci `yield`.

### Perbedaan `return` dan `yield`
```python
# Fungsi biasa (Return): Memori menampung SEMUA angka sekaligus
def buat_list(batas):
    hasil = []
    for i in range(batas):
        hasil.append(i)
    return hasil # Mengembalikan 1 juta data sekaligus (Berat!)

# Generator (Yield): Memori hanya menampung 1 angka pada satu waktu
def buat_generator(batas):
    for i in range(batas):
        yield i # Menghentikan sementara fungsi, mengirim 1 angka, lalu lanjut lagi

# Cara memanggilnya di Data Engineering (misal membaca file CSV raksasa):
for angka in buat_generator(1000000):
    # Proses baris per baris secara aman
    if angka == 3:
        print("Hanya mencetak sebagian kecil, RAM tetap aman!")
        break
```

---

## 🎁 BAB 3: Decorators (Fungsi Pembungkus)
*Decorator* (ditandai dengan simbol `@`) memungkinkan Anda memodifikasi atau menambahkan perilaku pada suatu fungsi *tanpa* mengubah kode di dalam fungsi tersebut.

Di dunia Data Engineering, *decorator* paling sering digunakan untuk **Log Waktu Eksekusi (Timer)** atau **Mencoba Ulang (Retry)** jika API gagal dipanggil.

### Membuat Decorator `@timer`
```python
import time

# 1. Ini adalah cetakan Decorator-nya
def timer(fungsi_asli):
    def pembungkus(*args, **kwargs):
        waktu_mulai = time.time()
        
        # Menjalankan fungsi aslinya
        hasil = fungsi_asli(*args, **kwargs) 
        
        waktu_selesai = time.time()
        print(f"[{fungsi_asli.__name__}] selesai dalam {waktu_selesai - waktu_mulai:.4f} detik")
        return hasil
        
    return pembungkus

# 2. Cara menggunakannya: Cukup tempelkan @timer di atas fungsi apa pun!
@timer
def proses_etl_berat():
    print("Memulai ETL...")
    time.sleep(1) # Simulasi proses memakan waktu 1 detik
    print("ETL Selesai.")

# Saat fungsi dipanggil, otomatis akan diukur waktunya oleh decorator
proses_etl_berat()
```
