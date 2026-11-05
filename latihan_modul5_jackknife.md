# Latihan Modul 5 Komputasi Statistik
## Resampling Methods: Jackknife

Semua kode di bawah dijalankan di R 4.3.3. Fungsi jackknife mengikuti gaya di modul (hapus satu data, hitung ulang statistik, lalu hitung bias dan variansi), hanya ditambah beberapa nilai di hasil `list` agar mudah dianalisis.

## Fungsi Jackknife

```r
# Fungsi untuk menghitung rata-rata Jackknife
jackknife_mean <- function(data) {
  n <- length(data)
  mean_original <- mean(data)
  mean_jackknife <- numeric(n)

  for (i in 1:n) {
    data_jack <- data[-i]
    mean_jackknife[i] <- mean(data_jack)
  }

  # Estimasi Jackknife untuk mean
  mean_jackknife_estimate <- mean(mean_jackknife)
  bias <- mean_jackknife_estimate - mean_original
  variance <- sum((mean_jackknife - mean_jackknife_estimate)^2) / n

  list(mean_original = mean_original, mean_jackknife_values = mean_jackknife,
       mean_jackknife = mean_jackknife_estimate, bias = bias, variance = variance)
}

# Fungsi untuk menghitung median Jackknife
jackknife_median <- function(data) {
  n <- length(data)
  median_original <- median(data)
  median_jackknife <- numeric(n)

  for (i in 1:n) {
    data_jack <- data[-i]
    median_jackknife[i] <- median(data_jack)
  }

  # Estimasi Jackknife untuk median
  median_jackknife_estimate <- median(median_jackknife)
  bias <- median_jackknife_estimate - median_original
  variance <- sum((median_jackknife - median_jackknife_estimate)^2) / n

  list(median_original = median_original, median_jackknife_values = median_jackknife,
       median_jackknife = median_jackknife_estimate, bias = bias, variance = variance)
}
```

## Soal 1

Data berat 5 barang: {120, 122, 118, 121, 119}. Hitung estimasi jackknife dari mean dan median.

```r
# Data awal
data1 <- c(120, 122, 118, 121, 119)

# Hitung estimasi Jackknife untuk mean
result_mean1 <- jackknife_mean(data1)
result_mean1

# Hitung estimasi Jackknife untuk median
result_median1 <- jackknife_median(data1)
result_median1
```

Output:

```
$mean_original
[1] 120

$mean_jackknife_values
[1] 120.00 119.50 120.50 119.75 120.25

$mean_jackknife
[1] 120

$bias
[1] 0

$variance
[1] 0.125

$median_original
[1] 120

$median_jackknife_values
[1] 120.0 119.5 120.5 119.5 120.5

$median_jackknife
[1] 120

$bias
[1] 0

$variance
[1] 0.2
```

**Analisis:** Rata-rata berat barang adalah 120 dan estimasi jackknife-nya juga 120, sehingga biasnya 0. Median jackknife juga 120 dengan bias 0. Saat satu data dihapus, mean hanya bergeser di sekitar 119,5 sampai 120,5 dan median bergeser di rentang yang sama. Variansi jackknife untuk mean (0,125) dan median (0,2) sangat kecil. Jadi berat barang tergolong stabil, dan mean sedikit lebih stabil daripada median karena variansinya lebih kecil.

## Soal 2

Buat 30 data dengan mean 65 dan standar deviasi 1,25, lalu hitung estimasi jackknife dari mean dan median.

```r
# Generate data (seed dipakai agar hasil bisa direproduksi)
set.seed(123)
data2 <- rnorm(30, mean = 65, sd = 1.25)
data2

# Hitung estimasi Jackknife untuk mean
result_mean2 <- jackknife_mean(data2)
result_mean2

# Hitung estimasi Jackknife untuk median
result_median2 <- jackknife_median(data2)
result_median2
```

Output (ringkas):

```
$mean_original
[1] 64.94112

$mean_jackknife
[1] 64.94112

$bias
[1] 1.421085e-14

$variance
[1] 0.001728486

$median_original
[1] 64.90783

$median_jackknife
[1] 64.90783

$bias
[1] 0

$variance
[1] 0.03250884
```

**Analisis:** Data hasil `rnorm` punya mean sebenarnya 64,94 (dekat dengan 65) dan median 64,91. Estimasi jackknife untuk mean sama dengan mean aslinya, dengan bias yang praktis nol (1,4 x 10^-14, hanya selisih pembulatan komputer) dan variansi sangat kecil, yaitu 0,0017. Median juga bias 0, tetapi variansinya 0,0325, sekitar 19 kali lebih besar dari mean. Hasil ini wajar karena median hanya bergantung pada dua nilai tengah, sehingga hanya bisa berubah ke dua nilai (64,73 atau 65,09) saat satu data dihapus. Jadi untuk data normal ini, mean lebih stabil dibandingkan median.

## Soal 3

Gunakan dataset `mtcars`, anggap `mpg` sebagai waktu penyelesaian tugas (jam) oleh 32 tim. Tentukan kestabilan median jika satu tim dihilangkan.

```r
# Data awal
data3 <- mtcars$mpg

# Hitung estimasi Jackknife untuk median
result_median3 <- jackknife_median(data3)
result_median3

# Cek nilai median jackknife yang muncul
table(result_median3$median_jackknife_values)
```

Output (ringkas):

```
$median_original
[1] 19.2

$median_jackknife
[1] 19.2

$bias
[1] 0

$variance
[1] 0

19.2
  32
```

**Analisis:** Median waktu penyelesaian dari 32 tim adalah 19,2 jam. Setelah setiap tim dihapus satu per satu, median yang dihasilkan selalu 19,2 jam di semua 32 percobaan, sehingga bias dan variansinya sama-sama 0. Ini terjadi karena dua nilai tengah data (urutan ke-16 dan ke-17) sama-sama 19,2, jadi menghapus satu tim tidak mengubah nilai tengahnya. Kesimpulannya, median waktu penyelesaian sangat stabil dan tidak terpengaruh oleh hilangnya satu tim, termasuk tim dengan nilai ekstrem seperti 10,4 atau 33,9.
