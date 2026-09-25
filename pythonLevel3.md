# Buku Saku Python Level 3: Modularisasi & Fungsi (Functions)

Di dunia Data Engineering, Anda tidak mungkin menaruh 1000 baris kode secara telanjang berurutan. Saat menggunakan orkestrator seperti **Apache Airflow**, setiap tahapan (Extract, Transform, Load) harus dibungkus dalam sebuah "mangkuk" yang disebut Fungsi (*Function*). Fungsi membuat kode bisa dites, dipakai ulang, dan memisahkan masalah (jika transformasi gagal, kode ekstraksi tidak perlu diubah).

---

## 🛠️ BAB 1: Anatomi Fungsi Dasar (`def` & `return`)
Fungsi ibarat sebuah mesin pabrik: Anda memasukkan bahan baku (Parameter), mesin bekerja, lalu mengeluarkan produk jadi (`return`).

### 1. Membuat Fungsi Sederhana
Gunakan kata kunci `def` (define). Parameter *default* sangat berguna jika pengguna lupa memasukkan argumen.

```python
def clean_currency(raw_money, currency_symbol="Rp"):
    """
    Fungsi ini membersihkan string uang kotor menjadi integer.
    (Teks di dalam kutip tiga ini disebut Docstring, sangat disarankan 
    ditulis untuk dokumentasi).
    """
    clean_text = raw_money.replace(currency_symbol, "").replace(".", "").strip()
    return int(clean_text)

# Cara memanggilnya:
uang_1 = clean_currency("Rp500.000")          # Menggunakan default "Rp"
uang_2 = clean_currency("$1.500", "$")        # Menimpa default dengan "$"
print(uang_1, uang_2) # Output: 500000 1500
```

---

## 🧰 BAB 2: Parameter Fleksibel (`*args` & `**kwargs`)
Kadang Anda tidak tahu persis berapa banyak data atau konfigurasi yang akan dilempar ke dalam fungsi Anda. 

### 1. `*args` (Arguments - Tuple Fleksibel)
Digunakan saat Anda ingin menerima argumen tanpa nama dalam jumlah tak terbatas. Python akan membungkusnya menjadi *Tuple*.

```python
def hitung_total_revenue(*args):
    # args di sini akan berbentuk Tuple: (500, 300, 200)
    total = 0
    for nilai in args:
        total += nilai
    return total

print(hitung_total_revenue(500, 300, 200)) # Output: 1000
```

### 2. `**kwargs` (Keyword Arguments - Dictionary Fleksibel)
Sangat sering digunakan di Data Engineering untuk membaca konfigurasi *database* secara dinamis. Python akan membungkusnya menjadi *Dictionary*.

```python
def connect_to_db(**kwargs):
    # kwargs akan berwujud: {'host': 'localhost', 'port': 5432, 'user': 'admin'}
    host = kwargs.get('host', '127.0.0.1')
    user = kwargs.get('user', 'guest')
    print(f"Connecting to {host} as {user}...")

connect_to_db(host="localhost", port=5432, user="admin")
```

---

## 🌍 BAB 3: Lingkup Variabel (Local vs Global Scope)
**Hukum Emas DE:** Hindari menggunakan variabel *Global* sebisa mungkin. Jika dua *pipeline* berjalan bersamaan dan mengubah variabel *Global* yang sama, data Anda akan hancur (kondisi ini disebut *Race Condition*).

```python
grand_total = 0 # <-- Ini Variabel GLOBAL

def proses_data(uang):
    # 'uang' dan 'pajak' adalah Variabel LOKAL (hanya hidup di dalam fungsi)
    pajak = 0.11 
    return uang - (uang * pajak)

# print(pajak) <-- ERROR! Variabel pajak tidak dikenal di luar fungsi

# Untuk mengubah variabel Global dari dalam fungsi, hindari pemanggilan langsung.
# Lebih baik kembalikan nilai (return) dan perbarui di luar:
grand_total += proses_data(1000)
```

---

## ⚡ BAB 4: Fungsi Anonim & Fungsional Bawaan
Terkadang, membuat `def` terlalu memakan tempat untuk operasi matematika yang sangat sederhana.

### 1. `lambda` (Fungsi Satu Baris)
*Lambda* adalah fungsi tanpa nama (anonim) yang langsung mengembalikan nilai. Sering digunakan di dalam Pandas DataFrame atau proses sortir.

```python
# Fungsi biasa
def kuadrat(x):
    return x * x

# Lambda (hasilnya sama persis, tapi 1 baris)
kuadrat_cepat = lambda x: x * x
print(kuadrat_cepat(5)) # Output: 25
```

### 2. `map` & `filter` (Memproses List tanpa `for` loop)
Kombinasi `lambda` dengan `map`/`filter` sangat kuat dan cepat karena diproses langsung di level C (bahasa asli mesin Python).

```python
data_kotor = ["100", "200", "error", "300"]

# FILTER: Hanya simpan yang isinya BUKAN 'error'
data_difilter = list(filter(lambda x: x != "error", data_kotor))
# Hasil: ['100', '200', '300']

# MAP: Ubah semua isi list yang difilter tadi menjadi Integer
data_bersih = list(map(lambda x: int(x), data_difilter))
# Hasil: [100, 200, 300]
```

### 3. `zip` (Resleting List)
Saat Anda menarik data dari CSV lawas, biasanya *Header* (nama kolom) dan baris data (isi) ada di tempat terpisah. `zip` menggabungkan mereka menjadi *Dictionary* dengan sangat elegan.

```python
kolom = ["id", "nama", "status"]
baris = [101, "Arya", "Completed"]

# Menyatukan dua list menjadi dictionary seketika!
data_dict = dict(zip(kolom, baris))
print(data_dict) # Output: {'id': 101, 'nama': 'Arya', 'status': 'Completed'}
```
