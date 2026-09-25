# Buku Saku Python Level 4: Ketahanan Kode (Robustness & I/O)

Di dunia nyata, data selalu kotor (huruf masuk ke kolom angka), server API sering *down*, dan file CSV terpotong di tengah jalan. Data Engineer tidak boleh membiarkan *pipeline* mati total (*crash*) hanya karena satu baris data yang cacat. Kita harus menangkap *error*-nya, mencatatnya (log), dan melanjutkan proses ke baris berikutnya.

---

## 🛡️ BAB 1: Error Handling (`try`, `except`, `finally`)
Jangan biarkan Python menghentikan program Anda. Gunakan blok `try-except` untuk "mencoba" kode berisiko dan "menangkap" ledakannya jika gagal.

### 1. Menangkap Error Spesifik
Selalu tangkap *error* yang spesifik, jangan sekadar `except:`. Ini membantu Anda tahu pasti apa yang rusak.
```python
raw_revenues = ["500", "error_teks", "300"]
total = 0

for val in raw_revenues:
    try:
        # Mencoba mengubah teks menjadi angka
        angka = int(val)
        total += angka
    except ValueError:
        # Ditangkap jika 'val' adalah teks huruf yang tidak bisa di-int()
        print(f"Peringatan: Data cacat ditemukan dan diabaikan -> {val}")
        continue

print(f"Total akhir: {total}")
```

### 2. Blok `finally` (Pembersih Wajib)
Kode di dalam `finally` akan **selalu dieksekusi**, tidak peduli kodenya sukses atau meledak. Sangat penting untuk menutup koneksi *database* agar tidak meninggalkan *Zombie Process* di memori server.
```python
try:
    print("Membuka koneksi database...")
    # Simulasi error (misal server timeout atau pembagian salah)
    hasil = 100 / 0 
except ZeroDivisionError:
    print("Error: Terjadi kesalahan operasi di database!")
finally:
    print("Menutup koneksi database. (Selalu berjalan!)")
```

---

## 📂 BAB 2: Membaca & Menulis File (Context Managers)
Cara lama membuka file adalah `file = open('data.csv')`. Masalahnya, jika Anda lupa memanggil `file.close()`, file tersebut akan terkunci di RAM komputer (*Memory Leak*). Solusinya adalah Context Manager (`with`).

### 1. Membaca File secara Aman
Blok `with` akan otomatis menutup file begitu proses di dalamnya selesai atau jika terjadi *error*.
```python
# Mode 'r' untuk Read (Membaca)
try:
    with open("log_transaksi.csv", "r") as file:
        for baris in file:
            # Memproses baris demi baris sangat hemat RAM
            print(baris.strip())
except FileNotFoundError:
    print("Error: File log_transaksi.csv tidak ditemukan di folder ini.")
```

### 2. Menulis/Menyimpan File Log
```python
data_bersih = ["ID1,Sukses", "ID2,Gagal"]

# Mode 'w' untuk Write (menimpa/membuat baru)
# Mode 'a' untuk Append (menambah baris di bawah file yang sudah ada)
with open("hasil_cleansing.csv", "w") as file:
    file.write("transaction_id,status\n") # Menulis header
    for baris in data_bersih:
        file.write(f"{baris}\n") # \n digunakan agar pindah baris
```

---

## 🌐 BAB 3: Parsing Data JSON (Modul `json`)
Sebagai Data Engineer, Anda akan sering mengambil data dari API yang berwujud teks string JSON yang panjang. Anda harus mengubahnya menjadi Dictionary Python agar bisa diolah.

### 1. Dari String ke Dictionary (`loads`)
```python
import json # Modul bawaan Python, tidak perlu install

# Ini adalah tipe teks/string, bukan dictionary (perhatikan kutip luarnya)
api_response = '{"id": 101, "status": "COMPLETED", "revenue": 500}'

try:
    # loads = load string (mengubah teks JSON menjadi Dictionary)
    data_dict = json.loads(api_response)
    print(data_dict["status"]) # Output: COMPLETED
except json.JSONDecodeError:
    print("Error: Teks JSON dari API rusak atau formatnya salah.")
```

### 2. Dari Dictionary ke String JSON (`dumps`)
Biasanya digunakan saat Anda ingin melempar hasil olahan Anda kembali ke API tujuan (seperti mengunggah data ke sistem pelaporan).
```python
import json

rekap_harian = {"hotel": "Bali", "total_trx": 150}

# dumps = dump string (mengubah Dictionary kembali menjadi teks JSON)
json_kirim = json.dumps(rekap_harian)
print(json_kirim) # Output string: '{"hotel": "Bali", "total_trx": 150}'
```
