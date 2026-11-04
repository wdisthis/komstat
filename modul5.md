# Modul 5 Komputasi Statistik
## Resampling Methods: Jackknife

## Pendahuluan

Dalam statistika, sampel dapat digunakan untuk memberikan kesimpulan mengenai kondisi populasi dengan keuntungan menghemat waktu dan biaya. Namun, karena sampel yang diambil tidak mencakup semua populasi, terdapat standar deviasi, standar galat, dan selang kepercayaan dari setiap ekspresi statistik. Jika kita mengambil sampel yang berbeda, akan dihasilkan statistik deskriptif (mean, median, dll) yang berbeda pula. Karena standar galat dihitung berdasarkan sampel, estimasi dapat menjadi bias terhadap sampel.

Teknik resampling adalah melakukan pengambilan subsample (boleh data berulang) dari sampel, kemudian dihitung setiap statistik deskriptifnya. Pengambilan sampel berulang kali akan menghasilkan distribusi dari nilai statistik deskriptif. Distribusi ini dapat memberikan besaran empirik atau akurasi mengenai beberapa tes statistik.

Keunggulan teknik resampling:

1. **Lebih sedikit asumsi.** Metode resampling tidak mengharuskan distribusi normal dan ukuran sampel yang besar.
2. **Akurasi yang lebih besar.** Metode bootstrap pada praktiknya menghasilkan hasil yang lebih akurat.
3. **Bersifat umum.** Metode resampling sangat mirip untuk berbagai statistik dan tidak memerlukan formula baru untuk setiap statistik.

Beberapa teknik resampling: Teknik Bootstrap dan Teknik Jackknife. Teknik ini dapat digunakan ketika:

1. Menurunkan model statistik.
2. Model statistik terlalu rumit.
3. Model yang mungkin tidak cukup valid.
4. Ukuran presisi yang akurat dalam model statistik hanya dimungkinkan dengan besar n.

## Teknik Resampling: Jackknife

Metode Jackknife adalah metode resampling dengan menghapus satu data dari sampel. Dari sampel yang sudah dihapus satu data, dihitung mean dan median. Jika sampel memiliki n data, akan terdapat distribusi mean atau median sebanyak n, hasil dari penghitungan mean atau median dari sampel yang sudah dihapus satu data sebanyak n kali.

## Mengenerate Data

Fungsi `rnorm` di R digunakan untuk menghasilkan data acak yang terdistribusi normal (Gaussian). Ini adalah salah satu fungsi dasar dalam statistika untuk simulasi dan analisis data.

Contoh dalam R:

```r
rnorm(20, mean = 60, sd = 2.5)
```

## Sampling Data

Sampling adalah teknik penting dalam analisis data untuk memperoleh subset dari data atau populasi untuk analisis lebih lanjut. Beberapa teknik sampling yang bisa digunakan:

1. Sampling acak (random sampling)
2. Sampling berstrata
3. Sampling klaster
4. Sampling sistematis
5. Sampling bertahap
6. Sampling tidak acak
7. Sampling snowball

Contoh penggunaan dalam R:

```r
# Vektor data
data <- 1:10

# Sampling acak tanpa pengulangan
set.seed(123)
sample_data <- sample(data, size = 5)
print(sample_data)

# Sampling acak dengan pengulangan
sample_data_replacement <- sample(data, size = 5, replace = TRUE)
print(sample_data_replacement)
```

`set.seed()` adalah fungsi dalam R yang digunakan untuk mengatur nilai awal (seed) dari generator angka acak. Ini memastikan bahwa setiap kali kode yang melibatkan elemen acak dijalankan, hasil yang dihasilkan konsisten dan dapat direproduksi.

## Menghapus Data

Langkah-langkah yang dilakukan:

1. Tinjau data θ: {x1, x2, x3, ..., xn}.
2. Misalkan θi adalah data yang diperoleh dengan menghapus xi.
3. Dalam R, kita dapat memperoleh data hasil penghapusan pada indeks tertentu.

Contoh dalam R:

```r
nilai <- c(7, 8, 9, 10)
nilai[-1]  # menghilangkan data ke-1
nilai[-2]  # menghilangkan data ke-2
```

## Contoh Kasus 1

Misalkan terdapat data = {10, 20, 30, 40, 50}. Gunakan teknik Jackknife untuk menghitung rata-rata (mean) dari data ini setelah menghilangkan satu observasi pada satu waktu, kemudian hitung estimasi bias dan variabilitas dari estimasi tersebut.

```r
# Data awal
data <- c(10, 20, 30, 40, 50)

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

  list(mean_jackknife = mean_jackknife_estimate, bias = bias, variance = variance)
}

# Hitung estimasi Jackknife untuk mean
result_mean <- jackknife_mean(data)
result_mean
```

## Contoh Kasus 2

Kamu sedang mengevaluasi waktu yang dibutuhkan untuk menyelesaikan proyek dari lima tim berbeda dalam sebuah perusahaan. Waktu penyelesaian (dalam hari) yang dicatat adalah: {12, 15, 20, 18, 14}.

Gunakan teknik Jackknife untuk mengetahui seberapa stabil median waktu penyelesaian jika satu tim dihilangkan dari perhitungan.

```r
# Data awal
data <- c(12, 15, 20, 18, 14)

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

  list(median_jackknife = median_jackknife_estimate, bias = bias, variance = variance)
}

# Hitung estimasi Jackknife untuk median
result_median <- jackknife_median(data)
result_median
```
