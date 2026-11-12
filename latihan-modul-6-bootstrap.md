# Latihan Modul 6 Komputasi Statistik
## Resampling Methods: Bootstrap

---

## Catatan Pengerjaan

- Semua kode menggunakan **R**, sesuai dengan modul.
- Seed yang dipakai adalah `set.seed(123)`, konsisten dengan contoh di modul.
- Interval kepercayaan 95% dihitung dengan **metode persentil**, yaitu kuantil 2.5% dan 97.5% dari distribusi bootstrap, seperti pada modul.
- Setiap soal dijawab dengan urutan: **kode**, **hasil**, lalu **analisis**.

---

## Soal 1: Interval Kepercayaan 95% untuk Rata-rata

> Buatlah 30 data dengan mean 65 dan standar deviasi 1.25. Lalu gunakan teknik bootstrap untuk memperkirakan interval kepercayaan 95% untuk rata-rata data tersebut!

### Kode R

```r
# Membuat 30 data dengan mean = 65 dan sd = 1.25
set.seed(123)
z <- rnorm(30)
data <- 65 + 1.25 * (z - mean(z)) / sd(z)

# Cek mean dan sd data yang dibuat
cat(sprintf("Mean data: %.4f\n", mean(data)))
cat(sprintf("Standar deviasi data: %.4f\n", sd(data)))

# Parameter bootstrap
n_iterations <- 1000
n_size <- length(data)

# Menyimpan hasil bootstrap
bootstrap_means <- numeric(n_iterations)

for (i in 1:n_iterations) {
  sample_boot <- sample(data, size = n_size, replace = TRUE)
  bootstrap_means[i] <- mean(sample_boot)
}

# Hitung interval kepercayaan 95%
lower_bound <- quantile(bootstrap_means, 0.025)
upper_bound <- quantile(bootstrap_means, 0.975)

# Output hasil
cat(sprintf("Rata-rata data asli: %.4f\n", mean(data)))
cat(sprintf("Interval kepercayaan 95%% untuk rata-rata: (%.4f, %.4f)\n",
            lower_bound, upper_bound))
```

### Penjelasan Kode

- **`rnorm(30)`** membangkitkan 30 bilangan acak dari distribusi normal baku (mean 0, sd 1).
- **`65 + 1.25 * (z - mean(z)) / sd(z)`** mentransformasi data sehingga mean-nya tepat 65 dan standar deviasinya tepat 1.25. Transformasi ini dipakai supaya syarat soal terpenuhi secara eksak, bukan hanya perkiraan.
- Jumlah iterasi memakai **1000**, sama dengan contoh di modul. Soal ini tidak menentukan jumlah iterasi.

### Hasil

Data yang dibangkitkan (4 angka di belakang koma):

```
64.3459, 64.7667, 67.0461, 65.1499, 65.2248, 67.2453, 65.6473, 63.4481, 64.1849, 64.4922,
66.6197, 65.5185, 65.5707, 65.2010, 64.3518, 67.3368, 65.6944, 62.5542, 65.9537, 64.4576,
63.6994, 64.7823, 63.7527, 64.1313, 64.2636, 62.9109, 66.1275, 65.2554, 63.6098, 66.6576
```

| Ukuran | Nilai |
|---|---|
| Mean data | 65.0000 |
| Standar deviasi data | 1.2500 |
| Nilai minimum | 62.5542 |
| Nilai maksimum | 67.3368 |
| Mean distribusi bootstrap | 64.9843 |
| Standar error bootstrap | 0.2277 |
| Standar error teoretis (1.25 / √30) | 0.2282 |
| **CI 95% bootstrap (persentil)** | **(64.5454, 65.4376)** |
| CI 95% berbasis distribusi t (pembanding) | (64.5332, 65.4668) |

### Analisis

1. **Estimasi titik.** Rata-rata sampel tepat 65.0000, dan ini sudah sesuai dengan syarat soal.
2. **Interval kepercayaan.** Interval bootstrap adalah (64.55, 65.44). Interval ini memuat nilai 65 dengan cukup nyaman, dan lebarnya sekitar 0.89.
3. **Kesesuaian dengan teori.** Standar error bootstrap (0.2277) hampir sama dengan standar error teoretis (0.2282). Ini menunjukkan bahwa bootstrap menangkap ketidakpastian rata-rata dengan baik.
4. **Perbandingan dengan interval t.** Interval bootstrap (64.5454, 65.4376) hampir identik dengan interval t (64.5332, 65.4668). Selisihnya kecil, sekitar 0.01 di kedua batas. Ini wajar karena data dibangkitkan dari distribusi normal, yaitu kondisi ideal bagi interval t.
5. **Catatan.** Karena data sudah normal, bootstrap tidak memberikan keunggulan besar di sini. Keunggulannya lebih terasa pada data yang tidak normal atau statistik yang rumusnya rumit.

---

## Soal 2: Interval Kepercayaan 95% untuk Koefisien Regresi

> Misalkan terdapat dataset yang berisi data jam belajar (x) dan skor ujian (y) dari 10 mahasiswa. Jam belajar = {1, 2, ..., 10}, Skor ujian = {55, 60, 65, 70, 75, 80, 85, 90, 95, 100}. Gunakan teknik bootstrap untuk memperkirakan interval kepercayaan 95% untuk koefisien regresi dari model linear y = β₀ + β₁x (lakukan iterasi sebanyak 2000 kali)!

### Kode R

```r
# Data asli
x <- c(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
y <- c(55, 60, 65, 70, 75, 80, 85, 90, 95, 100)

set.seed(123)

# Parameter bootstrap
n_iterations <- 2000
n_size <- length(x)

# Menyimpan hasil bootstrap
bootstrap_slopes <- numeric(n_iterations)
bootstrap_intercepts <- numeric(n_iterations)

for (i in 1:n_iterations) {
  indices <- sample(1:n_size, size = n_size, replace = TRUE)
  x_sample <- x[indices]
  y_sample <- y[indices]
  model <- lm(y_sample ~ x_sample)
  bootstrap_intercepts[i] <- coef(model)[1]
  bootstrap_slopes[i] <- coef(model)[2]
}

# Hitung interval kepercayaan 95%
slope_lower <- quantile(bootstrap_slopes, 0.025)
slope_upper <- quantile(bootstrap_slopes, 0.975)
int_lower <- quantile(bootstrap_intercepts, 0.025)
int_upper <- quantile(bootstrap_intercepts, 0.975)

# Output hasil
model_asli <- lm(y ~ x)
cat(sprintf("Koefisien regresi data asli (β1): %.4f\n", coef(model_asli)[2]))
cat(sprintf("Intercept data asli (β0): %.4f\n", coef(model_asli)[1]))
cat(sprintf("Interval kepercayaan 95%% untuk β1: (%.4f, %.4f)\n",
            slope_lower, slope_upper))
cat(sprintf("Interval kepercayaan 95%% untuk β0: (%.4f, %.4f)\n",
            int_lower, int_upper))
```

### Hasil

| Ukuran | Nilai |
|---|---|
| Koefisien regresi data asli (β₁) | 5.0000 |
| Intercept data asli (β₀) | 50.0000 |
| Jumlah iterasi bootstrap | 2000 |
| Jumlah iterasi yang gagal (regresi tidak dapat dihitung) | 0 |
| Rata-rata koefisien bootstrap | 5.0000 |
| **CI 95% untuk β₁ (kemiringan)** | **(5.0000, 5.0000)** |
| **CI 95% untuk β₀ (intercept)** | **(50.0000, 50.0000)** |

### Analisis

1. **Estimasi titik.** Data asli memberikan β₁ = 5 dan β₀ = 50. Modelnya adalah **y = 50 + 5x**. Model ini cocok dengan data secara sempurna, karena setiap kenaikan 1 jam belajar menaikkan skor tepat 5 poin.
2. **Mengapa intervalnya sempit sekali.** Data ini tidak mengandung noise. Setiap titik (x, y) berada tepat di atas garis y = 50 + 5x. Akibatnya, setiap sampel bootstrap yang memuat minimal dua nilai x berbeda akan menghasilkan garis yang sama persis. Semua 2000 nilai β₁ bootstrap bernilai 5, sehingga interval-nya berupa satu titik.
3. **Tidak ada regresi yang gagal.** Peluang semua 10 indeks yang terambil sama adalah 10 × (1/10)¹⁰ ≈ 10⁻⁹. Peluang ini terlalu kecil untuk muncul dalam 2000 iterasi, sehingga semua regresi dapat dihitung.
4. **Pelajaran metodologi.** Interval bootstrap mengukur ketidakpastian dari variasi data. Jika data tidak mengandung variasi acak di sekitar garis, bootstrap tidak punya apa-apa untuk diukur, sehingga hasilnya wajar bernilai nol. Pada data nyata, residual selalu ada dan intervalnya akan melebar.
5. **Kesimpulan teknis.** Hasil ini membuktikan bahwa implementasi bootstrap sudah benar, karena estimasi titik direproduksi dengan tepat. Interval yang sempit sekali bukan kesalahan kode, melainkan konsekuensi dari data yang sempurna linear.

---

## Soal 3: Bootstrap Median Sepal.Length Setosa (Data Iris)

> Gunakan data iris.
> a) Pilih variabel Sepal.Length dari spesies setosa.
> b) Hitung nilai median sampel.
> c) Gunakan bootstrap dengan 2000 replikasi untuk memperkirakan sebaran median.
> d) Bandingkan hasil median bootstrap dengan median sampel.

### Kode R

```r
# a) Pilih variabel Sepal.Length dari spesies setosa
setosa_sepal <- iris$Sepal.Length[iris$Species == "setosa"]
cat(sprintf("Jumlah data: %d\n", length(setosa_sepal)))

# b) Median sampel
median_sample <- median(setosa_sepal)
cat(sprintf("Median sampel: %.2f\n", median_sample))

# c) Bootstrap dengan 2000 replikasi
set.seed(123)
n_iterations <- 2000
n_size <- length(setosa_sepal)
bootstrap_medians <- numeric(n_iterations)

for (i in 1:n_iterations) {
  sample_boot <- sample(setosa_sepal, size = n_size, replace = TRUE)
  bootstrap_medians[i] <- median(sample_boot)
}

# Ringkasan distribusi bootstrap median
boot_mean <- mean(bootstrap_medians)
boot_se <- sd(bootstrap_medians)
lower_bound <- quantile(bootstrap_medians, 0.025)
upper_bound <- quantile(bootstrap_medians, 0.975)

# d) Perbandingan
bias <- boot_mean - median_sample
cat(sprintf("Mean median bootstrap: %.4f\n", boot_mean))
cat(sprintf("Standar error bootstrap: %.4f\n", boot_se))
cat(sprintf("Bias (mean bootstrap - median sampel): %.4f\n", bias))
cat(sprintf("CI 95%% untuk median: (%.2f, %.2f)\n", lower_bound, upper_bound))
print(table(bootstrap_medians))
```

### Hasil

| Ukuran | Nilai |
|---|---|
| Jumlah data (setosa) | 50 |
| Mean Sepal.Length setosa | 5.006 |
| **Median sampel (b)** | **5.00** |
| Jumlah replikasi bootstrap | 2000 |
| Mean distribusi median bootstrap | 5.0120 |
| Standar error bootstrap (median) | 0.0494 |
| Bias (mean bootstrap − median sampel) | +0.0120 |
| **CI 95% bootstrap untuk median** | **(4.90, 5.10)** |
| Proporsi replikasi yang median-nya tepat 5.0 | 0.6615 |

Sebaran median bootstrap (jumlah dari 2000 replikasi):

| Nilai median | 4.80 | 4.85 | 4.90 | 4.95 | **5.00** | 5.05 | 5.10 | 5.20 |
|---|---|---|---|---|---|---|---|---|
| Frekuensi | 5 | 2 | 110 | 78 | **1323** | 164 | 317 | 1 |

### Analisis

**Bagian (a) dan (b).** Data setosa berjumlah 50 nilai, dan rata-ratanya 5.006. Karena jumlah datanya genap, median adalah rata-rata dari dua nilai di tengah. Setelah diurutkan, nilai ke-25 dan ke-26 keduanya bernilai 5.0, sehingga median sampel = **5.00**.

**Bagian (c).** Distribusi median bootstrap berpusat di sekitar 5.0. Nilai 5.0 muncul pada 1323 dari 2000 replikasi, atau sekitar 66%. Nilai di sekitarnya (4.9, 4.95, 5.05, 5.1) muncul dengan frekuensi yang jauh lebih kecil. Standar error bootstrap sebesar 0.0494 menunjukkan bahwa median hanya bergeser sedikit dari sampel ke sampel.

**Bagian (d).** Perbandingannya sebagai berikut.
- **Median bootstrap rata-rata 5.0120**, sedangkan **median sampel 5.00**. Selisihnya hanya 0.012, atau sekitar seperempat dari standar error. Secara praktis keduanya hampir sama.
- Bias yang kecil ini wajar, karena bootstrap hanya mengambil ulang dari 50 data yang sama. Sebaran nilai sampel tidak simetris (misalnya nilai 5.1 muncul 8 kali, sedangkan 4.9 muncul 4 kali), dan ketidaksimetrisan inilah yang membuat rata-rata median bootstrap sedikit berbeda dari median sampel.
- **Nilai median bootstrap bersifat diskret**, karena data ditulis dengan satu desimal. Median bisa berupa 4.85 atau 4.95 jika dua nilai tengahnya berbeda. Ini menjelaskan mengapa ada frekuensi di nilai-nilai tersebut.
- **Interval kepercayaan 95% (4.90, 5.10)** menunjukkan bahwa median populasi kemungkinan besar berada di kisaran 4.9 sampai 5.1 cm.

---

## Kesimpulan

1. **Soal 1 (rata-rata data sintetis).** Dengan 1000 iterasi bootstrap, interval kepercayaan 95% untuk rata-rata adalah **(64.55, 65.44)**. Interval ini memuat nilai 65, dan hasilnya sangat dekat dengan interval berbasis distribusi t. Bootstrap bekerja dengan baik untuk rata-rata data yang berdistribusi normal.

2. **Soal 2 (koefisien regresi).** Dengan 2000 iterasi, interval kepercayaan 95% untuk β₁ adalah **(5, 5)** dan untuk β₀ adalah **(50, 50)**. Hasil ini tidak menunjukkan ketidakpastian karena datanya sempurna linear. Hasil ini membuktikan bahwa kode bootstrap sudah benar, tetapi interval yang sempit tidak boleh diartikan sebagai ketelitian tinggi pada data nyata.

3. **Soal 3 (median iris setosa).** Median sampel Sepal.Length setosa adalah **5.00**. Dengan 2000 replikasi, median bootstrap rata-rata **5.012** dengan standar error **0.049** dan interval kepercayaan 95% **(4.90, 5.10)**. Median sampel dan median bootstrap hampir identik, sehingga median sampel dapat dipercaya sebagai estimasi titik. Interval bootstrap menunjukkan bahwa median ini cukup stabil, dan sebarannya sempit.

4. **Pelajaran utama dari ketiga soal.** Bootstrap dapat memperkirakan ketidakpastian untuk statistik apa pun, termasuk rata-rata, koefisien regresi, dan median, tanpa asumsi distribusi yang kuat. Lebar interval bergantung pada seberapa besar variasi yang ada dalam data. Data yang sempurna menghasilkan interval nol, sedangkan data yang bervariasi menghasilkan interval yang lebih lebar.
