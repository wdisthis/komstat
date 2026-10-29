# Latihan Modul 4 Pengembangan Pemrograman Fungsi R

2025-10-26

Kerjakan soal latihan dengan benar dan tepat.

---

## Soal 1

Buatlah fungsi volume_tabung di R yang menghitung volume sebuah tabung berdasarkan jari-jari (r) dan tinggi (t). Fungsi harus mengembalikan nilai volume tabung tersebut.

**Jawaban**

```r
# Membuat fungsi untuk menghitung volume tabung
volume_tabung <- function(r, t) {
  pi * r^2 * t
}

# Memanggil fungsi dengan jari-jari 7 dan tinggi 10
volume_tabung(7, 10)
```

```
## [1] 1539.38
```

**Analisis**

Kode R di atas menunjukkan cara membuat fungsi untuk menghitung volume tabung. Pertama, didefinisikan fungsi bernama volume_tabung yang memiliki dua parameter, yaitu r (jari-jari) dan t (tinggi). Di dalam fungsi, digunakan rumus volume tabung, yaitu π × r² × t, dengan pi sebagai konstanta bawaan R dan r^2 sebagai operasi pangkat dua. Karena R secara otomatis mengembalikan nilai dari ekspresi terakhir dalam fungsi, hasil perhitungan langsung dikembalikan tanpa perlu menuliskan return(). Saat volume_tabung(7, 10) dipanggil, perhitungannya menjadi 3,14159 × 49 × 10 sehingga menghasilkan output 1539.38. Dengan fungsi ini, perhitungan volume untuk ukuran tabung yang lain cukup dilakukan dengan mengganti nilai argumen tanpa menulis ulang rumusnya.

---

## Soal 2

Buatlah sebuah fungsi bernama fibonacci() yang menerima sebuah bilangan bulat n. Fungsi tersebut harus menghitung dan mengembalikan suku ke-n dari deret Fibonacci menggunakan rekursi.

**Jawaban**

```r
# Membuat fungsi rekursi untuk menghitung suku ke-n deret Fibonacci
fibonacci <- function(n) {
  if (n <= 1) { # base case
    return(n)
  } else {
    return(fibonacci(n - 1) + fibonacci(n - 2)) # recursive call
  }
}

# Menghitung suku ke-10
fibonacci(10)

# Menampilkan 11 suku pertama (suku ke-0 sampai ke-10)
sapply(0:10, fibonacci)
```

```
## [1] 55
##  [1]  0  1  1  2  3  5  8 13 21 34 55
```

**Analisis**

Kode R di atas menunjukkan penerapan fungsi rekursi untuk menghitung suku ke-n dari deret Fibonacci. Fungsi fibonacci() menerima satu parameter n, lalu memeriksa kondisi dasar (base case), yaitu ketika n <= 1 fungsi langsung mengembalikan n sendiri, karena suku ke-0 bernilai 0 dan suku ke-1 bernilai 1. Jika n lebih besar dari 1, fungsi memanggil dirinya sendiri dua kali, yaitu fibonacci(n - 1) dan fibonacci(n - 2), kemudian menjumlahkan hasilnya sesuai definisi deret Fibonacci, yaitu setiap suku merupakan jumlah dua suku sebelumnya. Saat fibonacci(10) dijalankan, proses rekursi terus berulang hingga mencapai base case dan menghasilkan 55. Fungsi sapply(0:10, fibonacci) digunakan untuk menerapkan fungsi tersebut ke setiap nilai 0 sampai 10 sehingga terbentuk deret 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55. Perlu diketahui bahwa rekursi ini akan melambat untuk nilai n yang besar karena banyak perhitungan yang diulang, tetapi sudah sesuai untuk menggambarkan konsep rekursi.

---

## Soal 3

Buatlah sebuah class S3 bernama "Barang" yang memiliki lima variabel yaitu nama, kode, harga, jumlah, dan diskon. Selanjutnya, buatlah sebuah fungsi hitung_total() yang berfungsi untuk menghitung total harga barang setelah diskon diterapkan. Dan buatlah fungsi generik info() yang menampilkan informasi lengkap mengenai barang tersebut termasuk total harga setelah diskon.

**Jawaban**

```r
# Membuat objek berupa list dengan lima variabel
brg1 <- list(
  nama = "Laptop",
  kode = "BRG001",
  harga = 8000000,
  jumlah = 2,
  diskon = 10 # dalam persen
)

# Menambahkan atribut class agar menjadi objek S3 "Barang"
class(brg1) <- "Barang"

# Membuat fungsi untuk menghitung total harga setelah diskon
hitung_total <- function(obj) {
  total_kotor <- obj$harga * obj$jumlah
  total_kotor - (total_kotor * obj$diskon / 100)
}

# Membuat fungsi generik 'info'
info <- function(obj) {
  UseMethod("info") # Menentukan bahwa 'info' adalah fungsi generik
}

# Membuat method khusus untuk class 'Barang'
info.Barang <- function(obj) {
  cat("Nama Barang  :", obj$nama, "\n")
  cat("Kode Barang  :", obj$kode, "\n")
  cat("Harga Satuan : Rp", format(obj$harga, big.mark = ".", decimal.mark = ",", scientific = FALSE), "\n")
  cat("Jumlah       :", obj$jumlah, "\n")
  cat("Diskon       :", obj$diskon, "%\n")
  cat("Total Harga  : Rp", format(hitung_total(obj), big.mark = ".", decimal.mark = ",", scientific = FALSE), "\n")
}

# Memanggil fungsi generik
info(brg1)
```

```
## Nama Barang  : Laptop 
## Kode Barang  : BRG001 
## Harga Satuan : Rp 8.000.000 
## Jumlah       : 2 
## Diskon       : 10 %
## Total Harga  : Rp 14.400.000
```

**Analisis**

Kode R di atas menunjukkan cara membuat objek S3 bernama "Barang" lengkap dengan fungsi perhitungan dan fungsi generik. Pertama, dibuat sebuah list brg1 yang berisi lima variabel, yaitu nama, kode, harga, jumlah, dan diskon, kemudian list tersebut diubah menjadi objek kelas "Barang" dengan class(brg1) <- "Barang". Selanjutnya, fungsi hitung_total() dibuat untuk menghitung total harga setelah diskon, yaitu dengan mengalikan harga dan jumlah untuk mendapatkan total kotor, lalu dikurangi besar diskon sebesar total kotor × diskon / 100. Fungsi generik info() dibuat menggunakan UseMethod("info"), dan method khusus info.Barang() didefinisikan agar menampilkan seluruh informasi barang beserta total harga setelah diskon. Fungsi format() dengan argumen big.mark digunakan agar angka tampil dengan pemisah ribuan dan tidak berubah menjadi notasi ilmiah seperti 8e+06. Saat info(brg1) dipanggil, R mengenali kelas objek dan menjalankan info.Barang(), sehingga menghasilkan total harga Rp 14.400.000, yaitu hasil dari (8.000.000 × 2) dikurangi diskon 10%.

---

## Soal 4

Buatlah sebuah class S4 bernama Pegawai yang memiliki tiga slot yaitu nama (character), gaji_pokok (numeric), dan tunjangan (numeric). Kemudian buatlah objek dari class tersebut berisi data seorang pegawai. Setelah itu, buatlah fungsi generik info() dan method info() khusus untuk class Pegawai yang menampilkan informasi nama pegawai, gaji pokok, tunjangan, serta total gaji (hasil penjumlahan gaji pokok dan tunjangan) dengan format yang rapi.

**Jawaban**

```r
# Membuat class S4 bernama 'Pegawai'
setClass(
  "Pegawai",
  slots = list(
    nama = "character",
    gaji_pokok = "numeric",
    tunjangan = "numeric"
  )
)

# Membuat objek dari class 'Pegawai'
pgw1 <- new("Pegawai", nama = "Budi Santoso", gaji_pokok = 5000000, tunjangan = 1500000)

# Membuat fungsi generik 'info'
setGeneric("info", function(obj) standardGeneric("info"))

# Membuat method 'info' khusus untuk class 'Pegawai'
setMethod("info",
  "Pegawai",
  function(obj) {
    total_gaji <- obj@gaji_pokok + obj@tunjangan
    cat("==============================\n")
    cat("Nama Pegawai :", obj@nama, "\n")
    cat("Gaji Pokok   : Rp", format(obj@gaji_pokok, big.mark = ".", decimal.mark = ",", scientific = FALSE), "\n")
    cat("Tunjangan    : Rp", format(obj@tunjangan, big.mark = ".", decimal.mark = ",", scientific = FALSE), "\n")
    cat("Total Gaji   : Rp", format(total_gaji, big.mark = ".", decimal.mark = ",", scientific = FALSE), "\n")
    cat("==============================\n")
  }
)

# Memanggil fungsi generik
info(pgw1)
```

```
## [1] "info"
```

```
## ==============================
## Nama Pegawai : Budi Santoso 
## Gaji Pokok   : Rp 5.000.000 
## Tunjangan    : Rp 1.500.000 
## Total Gaji   : Rp 6.500.000 
## ==============================
```

**Analisis**

Kode R di atas menunjukkan penggunaan sistem S4 untuk membuat class dan method secara berorientasi objek. Pertama, dibuat class S4 bernama "Pegawai" menggunakan setClass() dengan tiga slot, yaitu nama bertipe character, serta gaji_pokok dan tunjangan yang bertipe numeric, sehingga isi objek divalidasi sesuai tipe yang ditentukan. Kemudian dibuat objek pgw1 dari class tersebut menggunakan new() dengan data seorang pegawai bernama Budi Santoso, gaji pokok 5.000.000, dan tunjangan 1.500.000. Selanjutnya dibuat fungsi generik info menggunakan setGeneric(), lalu method khusus untuk class "Pegawai" dibuat menggunakan setMethod(). Di dalam method, total gaji dihitung dengan menjumlahkan slot gaji_pokok dan tunjangan yang diakses melalui operator @, kemudian seluruh informasi ditampilkan menggunakan cat() dengan garis pembatas dan format() agar tampilan rapi. Saat info(pgw1) dipanggil, R mengenali bahwa pgw1 adalah objek dari class "Pegawai" dan menjalankan method yang sesuai sehingga menghasilkan total gaji Rp 6.500.000.

> Catatan: jika soal 3 dan soal 4 dijalankan dalam satu sesi R yang sama, `info` sudah ada sebagai fungsi S3 sehingga `setGeneric()` dapat menampilkan pesan tambahan saat membuat generik S4. Hal ini tidak memengaruhi hasil, tetapi agar bersih sebaiknya kedua soal dijalankan di sesi yang terpisah.

---

## Soal 5

Buatlah sebuah class R6 bernama Karyawan yang memiliki dua atribut yaitu nama dan gaji_pokok. Tambahkan metode hitung_gaji() untuk menghitung total gaji karyawan dengan menambahkan tunjangan sebesar 10% dari gaji pokok. Selanjutnya, buatlah objek dari kelas tersebut isi dengan data seorang karyawan dan panggil metode hitung_gaji() untuk menampilkan total gaji yang diterima.

**Jawaban**

```r
# Memanggil library R6
library(R6)

# Membuat class R6 bernama 'Karyawan'
Karyawan <- R6Class("Karyawan",
  public = list(
    nama = NULL,
    gaji_pokok = NULL,

    # Konstruktor (fungsi yang dijalankan saat objek dibuat)
    initialize = function(nama, gaji_pokok) {
      self$nama <- nama
      self$gaji_pokok <- gaji_pokok
    },

    # Method untuk menghitung total gaji (gaji pokok + tunjangan 10%)
    hitung_gaji = function() {
      tunjangan <- 0.10 * self$gaji_pokok
      total_gaji <- self$gaji_pokok + tunjangan
      cat("Nama Karyawan :", self$nama, "\n")
      cat("Gaji Pokok    : Rp", format(self$gaji_pokok, big.mark = ".", decimal.mark = ",", scientific = FALSE), "\n")
      cat("Tunjangan 10% : Rp", format(tunjangan, big.mark = ".", decimal.mark = ",", scientific = FALSE), "\n")
      cat("Total Gaji    : Rp", format(total_gaji, big.mark = ".", decimal.mark = ",", scientific = FALSE), "\n")
      invisible(total_gaji)
    }
  )
)

# Membuat objek dari class Karyawan
k1 <- Karyawan$new("Siti Aminah", 6000000)

# Memanggil method hitung_gaji()
k1$hitung_gaji()
```

```
## Nama Karyawan : Siti Aminah 
## Gaji Pokok    : Rp 6.000.000 
## Tunjangan 10% : Rp 600.000 
## Total Gaji    : Rp 6.600.000
```

**Analisis**

Kode R di atas menunjukkan penggunaan class R6 untuk membuat objek berorientasi objek di R. Pertama, library R6 dipanggil, lalu dibuat class Karyawan menggunakan R6Class() dengan dua atribut, yaitu nama dan gaji_pokok, serta konstruktor initialize() yang mengisi kedua atribut tersebut saat objek dibuat. Method hitung_gaji() kemudian dibuat untuk menghitung tunjangan sebesar 10% dari gaji pokok (0.10 × gaji_pokok), menjumlahkannya dengan gaji pokok untuk mendapatkan total gaji, lalu menampilkan seluruh rinciannya menggunakan cat(). Fungsi invisible(total_gaji) digunakan agar nilai total gaji tetap dapat disimpan ke variabel bila diperlukan, tanpa tercetak dua kali di konsol. Objek k1 dibuat dari class ini menggunakan $new("Siti Aminah", 6000000), sehingga k1 memiliki nama "Siti Aminah" dan gaji pokok 6.000.000. Saat k1$hitung_gaji() dipanggil, method menampilkan tunjangan sebesar Rp 600.000 dan total gaji yang diterima sebesar Rp 6.600.000.
