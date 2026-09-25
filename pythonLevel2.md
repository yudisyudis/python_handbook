# Buku Saku Python Level 2: Struktur Data Inti (The Big Four)

Di SQL, data disimpan di dalam "Tabel". Di Python, sebelum data tersebut dimasukkan ke *database* atau diolah menjadi file, data hidup di dalam memori komputer (*RAM*). Untuk menampung dan mengatur data di memori, kita menggunakan 4 struktur data inti ini. 

Menguasai keempatnya adalah syarat mutlak menangani JSON dari API atau menggantikan fungsi SQL saat dilarang menggunakan Pandas.

---

## 📚 BAB 1: List (Daftar Antrean Dinamis)
List adalah tempat penampungan paling umum. Sifatnya *mutable* (bisa diubah, ditambah, dikurangi) dan menjaga urutan data masuk. Di Data Engineering, List biasanya merepresentasikan baris-baris data dari tabel/CSV.

### 1. Membuat dan Mengakses List
Ingat, indeks di Python selalu dimulai dari `0`.
```python
# List of strings (mirip kolom di CSV)
kolom_tabel = ["user_id", "booking_date", "revenue", "status"]

# Mengambil elemen pertama dan terakhir
print(kolom_tabel[0])    # Output: "user_id"
print(kolom_tabel[-1])   # Output: "status" (minus 1 berarti dari belakang)
```

### 2. Menambah Data (Append vs Extend)
Ini sering menjebak saat tes *coding*.
- `.append()` memasukkan elemen baru sebagai **satu kesatuan**.
- `.extend()` memecah elemen baru dan menggabungkannya ke daftar utama.

```python
data_master = ["A", "B"]
data_baru = ["C", "D"]

data_master.append(data_baru) 
# Hasil salah/nested: ['A', 'B', ['C', 'D']] -> Hati-hati, ini sering terjadi!

data_master = ["A", "B"]
data_master.extend(data_baru) 
# Hasil benar (flat): ['A', 'B', 'C', 'D']
```

---

## 📖 BAB 2: Dictionary (Kamus Key-Value)
Jika List menggunakan angka indeks (0, 1, 2) untuk mencari data, Dictionary menggunakan **Key** (kata kunci) bernama khusus. 
Bagi Data Engineer, Dictionary adalah dewa. Ini digunakan untuk **membaca JSON API** dan **melakukan simulasi `GROUP BY`** di memori.

### 1. Struktur Dasar dan Nested Dictionary (JSON)
```python
# Bentuk dasar Dictionary (mirip baris data dengan nama kolomnya)
user_data = {
    "id": 101,
    "name": "Yudisthira",
    "is_active": True
}

print(user_data["name"]) # Output: Yudisthira
```

### 2. Menghindari Ranjau `KeyError` dengan `.get()`
Saat menarik data dari API, kadang ada kolom yang hilang (*missing data*). Jika Anda memanggil `user_data["address"]` dan kuncinya tidak ada, program akan *crash* (`KeyError`). Selalu gunakan `.get()`!

```python
# Jika 'address' tidak ada, kembalikan teks "Unknown" (Tidak Error)
kota = user_data.get("address", "Unknown") 
print(kota) # Output: Unknown
```

### 3. Simulasi SQL `GROUP BY` dengan Dictionary
Bagaimana menghitung total *revenue* per hotel tanpa SQL?
```python
data_mentah = [
    ("Hotel_A", 500), 
    ("Hotel_B", 300), 
    ("Hotel_A", 200)
]

total_revenue = {} # Siapkan Dictionary kosong

for hotel, uang in data_mentah:
    if hotel in total_revenue:
        total_revenue[hotel] += uang  # Jika sudah ada, tambahkan uangnya
    else:
        total_revenue[hotel] = uang   # Jika belum ada, buat baru

print(total_revenue) # Output: {'Hotel_A': 700, 'Hotel_B': 300}
```

---

## 📦 BAB 3: Tuple (Kapsul Permanen)
Tuple mirip seperti List, tapi bersifat **Immutable** (TIDAK BISA diubah setelah dibuat). Anda tidak bisa me-`.append()` atau mengganti isinya. 

### 1. Mengapa menggunakan Tuple?
Karena tidak bisa diubah, Tuple sangat hemat memori dan aman digunakan untuk data yang sifatnya statis (seperti kredensial *database* atau koordinat sistem).

```python
# Ditandai dengan tanda kurung biasa ()
db_config = ("localhost", "5432", "admin", "password123")

# db_config[0] = "192.168.1.1" --> Ini akan menghasilkan ERROR (TypeError)
```

### 2. Tuple Unpacking (Membongkar Isi)
Sangat sering digunakan saat mengembalikan nilai ganda dari fungsi pembersih data.
```python
transaksi = ("ID-101", "Completed", 500000)

# Langsung pecah ke 3 variabel berbeda (Unpacking)
trans_id, status, uang = transaksi 
print(uang) # Output: 500000
```

---

## 🎯 BAB 4: Set (Himpunan Unik)
Set adalah kumpulan data yang **tidak memiliki urutan** dan **TIDAK BOLEH ADA DUPLIKAT**.
Di Data Engineering, Set digunakan untuk menghapus duplikasi data dalam sekejap dan mencari perbedaan data antar 2 tabel/sistem (*Data Reconciliation*).

### 1. Menghapus Duplikat Seketika
```python
id_kotor = [1, 2, 2, 3, 3, 3, 4]
id_bersih = list(set(id_kotor))
print(id_bersih) # Output: [1, 2, 3, 4]
```

### 2. Mencari Perbedaan (Delta) antar 2 Sistem
Misal kita ingin mengecek data mana yang ada di CSV tapi belum masuk ke Database.
```python
id_di_csv = {101, 102, 103, 104}
id_di_database = {101, 102, 103}

# Mencari selisih (Data di CSV dikurangi data di Database)
belum_masuk = id_di_csv - id_di_database 
print(belum_masuk) # Output: {104}

# Mencari irisan (Data yang sama-sama ada di keduanya)
sama = id_di_csv.intersection(id_di_database)
print(sama) # Output: {101, 102, 103}
```
