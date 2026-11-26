# Modul Praktikum 8 — Komputasi Statistik
## Numerical Integration 2: Metode Simpson 3/8, Boole, dan Romberg

**Tim Dosen Komputasi Statistik** · Program Studi Sains Data, Institut Teknologi Sumatera

> **Catatan penyusun.** Modul ini disusun ulang dari modul asli, lengkap dengan jawaban seluruh contoh, latihan, dan tugas praktikum beserta analisisnya. Nilai numerik dihitung dengan algoritma yang sama persis dengan kode R pada modul (diverifikasi dengan Python/SciPy karena R tidak tersedia di lingkungan penyusunan). Nilai eksak diambil dari integrasi adaptif berpresisi tinggi (setara `integrate()` pada R). Selisih pada digit ke-10 ke atas dapat muncul karena toleransi `integrate()` di R.
>
> **Dua koreksi penting pada kode modul asli** (dijelaskan di Bagian 3.2 dan 3.4):
> 1. Kode Boole pada modul asli **tidak memberi koefisien 14** pada titik-titik dalam kelipatan 4, sehingga hasilnya salah besar.
> 2. Kode Romberg pada modul asli bermasalah di iterasi k = 1 karena `fx[2:n]` dengan n = 1 menghasilkan `c(2, 1)`.

---

## Capaian Pembelajaran

Setelah menyelesaikan praktikum ini, mahasiswa diharapkan mampu:

1. Menjelaskan ide dasar metode Simpson 3/8, Boole, dan Romberg.
2. Menurunkan rumus dan memahami bentuk komposit dari ketiga metode.
3. Mengimplementasikan algoritma tiap metode menggunakan bahasa R.
4. Membandingkan hasil aproksimasi terhadap nilai eksak integral.

---

## 1. Pendahuluan

Pada pertemuan sebelumnya (*Numerical Integration Part 1*) telah dibahas ide dasar aproksimasi integral menggunakan partisi selang $[a,b]$ dan metode:

- Riemann (kiri, kanan, tengah),
- Trapesium (sederhana dan komposit),
- Simpson 1/3.

Pada pertemuan ini kita melanjutkan ke metode dengan orde lebih tinggi, yaitu **Simpson 3/8, Boole, dan Romberg**. Metode-metode ini:

- menggunakan lebih banyak titik evaluasi fungsi,
- menghasilkan galat yang lebih kecil untuk lebar langkah $h$ yang sama,
- banyak digunakan dalam praktik integrasi numerik tingkat lanjut.

---

## 2. Dasar Teori

### 2.1 Metode Simpson 3/8

Simpson 3/8 adalah metode Newton–Cotes berderajat tiga. Untuk satu panel, interval $[a,b]$ dibagi menjadi tiga sub-selang sama panjang:

$$h=\frac{b-a}{3},\qquad x_0=a,\; x_1=a+h,\; x_2=a+2h,\; x_3=b.$$

Rumus satu panel:

$$\int_a^b f(x)\,dx \approx \frac{3h}{8}\Big[f(x_0)+3f(x_1)+3f(x_2)+f(x_3)\Big].$$

Untuk bentuk komposit, $[a,b]$ dibagi menjadi $n$ sub-selang dengan **$n$ kelipatan 3** dan $h=\frac{b-a}{n}$:

$$\int_a^b f(x)\,dx \approx \frac{3h}{8}\left[f(x_0)+f(x_n)+3\!\!\sum_{\substack{i=1\\ i\not\equiv 0\,(\mathrm{mod}\,3)}}^{n-1}\!\! f(x_i)+2\!\!\sum_{\substack{i=3\\ i\equiv 0\,(\mathrm{mod}\,3)}}^{n-3}\!\! f(x_i)\right].$$

Pola koefisien: `1, 3, 3, 2, 3, 3, 2, ..., 3, 3, 1`. Koefisien 2 muncul karena titik batas antarpanel dipakai dua kali (1 + 1).

### 2.2 Metode Boole

Boole adalah Newton–Cotes orde 4 yang menggunakan 5 titik berjarak sama $h=\frac{b-a}{4}$:

$$\int_a^b f(x)\,dx \approx \frac{2h}{45}\Big[7f(x_0)+32f(x_1)+12f(x_2)+32f(x_3)+7f(x_4)\Big].$$

Untuk bentuk komposit, $[a,b]$ dibagi menjadi $n$ sub-selang dengan **$n$ kelipatan 4**, $h=\frac{b-a}{n}$. Pola koefisiennya:

```
7, 32, 12, 32, 14, 32, 12, 32, 14, ..., 32, 12, 32, 7
```

Koefisien **14** pada titik batas antarpanel berasal dari $7+7$ (sama seperti koefisien 2 pada Simpson 3/8).

### 2.3 Metode Romberg

Metode Romberg membangun tabel aproksimasi dari aturan trapesium komposit dengan jumlah panel yang berlipat dua, lalu menerapkan **Richardson Extrapolation**. Misalkan $R_{k,1}$ adalah hasil aturan trapesium dengan $n=2^{k-1}$ sub-selang:

$$R_{k,1}=T(h_k),\qquad h_k=\frac{b-a}{2^{k-1}}.$$

Elemen lainnya dihitung dengan:

$$R_{k,j}=R_{k,j-1}+\frac{R_{k,j-1}-R_{k-1,j-1}}{4^{\,j-1}-1},\qquad j=2,\dots,k.$$

Skema tabel Romberg (setiap kolom adalah ekstrapolasi dari kolom sebelumnya):

```
R1,1
R2,1  R2,2
R3,1  R3,2  R3,3
R4,1  R4,2  R4,3  R4,4
 ...
```

Fakta yang berguna: $R_{k,2}$ identik dengan Simpson 1/3 dan $R_{k,3}$ identik dengan aturan Boole pada partisi yang sama.

---

## 3. Algoritma dan Implementasi

### 3.1 Algoritma Simpson 3/8

1. Input $f(x)$, batas $a,b$, jumlah sub-selang $n$ (kelipatan 3).
2. Hitung $h=\frac{b-a}{n}$.
3. Bentuk titik $x_i=a+ih$, $i=0,1,\dots,n$.
4. Hitung $f(x_i)$ untuk semua $i$.
5. Inisialisasi $S\leftarrow f(x_0)+f(x_n)$.
6. Untuk $i=1,\dots,n-1$: jika $i\equiv 0 \pmod 3$ maka $S\leftarrow S+2f(x_i)$, selain itu $S\leftarrow S+3f(x_i)$.
7. $I\leftarrow \frac{3h}{8}S$.

### Contoh 1 — Simpson 3/8

Hitung

$$I_1=\int_0^3\left(e^{-x}\sin(x^2)+3x^2\right)dx$$

dengan $n=300$. Bandingkan dengan `integrate()` dan catat galat absolutnya.

### 3.2 Kode R Simpson 3/8

```r
###############################################################
# METODE SIMPSON 3/8
# Numerical Integration Part 2 - Komputasi Statistik
###############################################################

# Definisi fungsi (bisa diganti)
f <- function(x) exp(-x) * sin(x^2) + 3 * x^2

# Batas integrasi
a <- 0
b <- 3

# Jumlah sub-selang (harus kelipatan 3)
n <- 300

# Lebar langkah
h <- (b - a) / n

# Titik x dan nilai f(x)
x  <- numeric(n + 1)
fx <- numeric(n + 1)

for (i in 0:n) {
  x[i + 1]  <- a + i * h
  fx[i + 1] <- f(x[i + 1])
}

# Rumus Simpson 3/8 Komposit
S <- fx[1] + fx[n + 1]

for (i in 2:n) {
  if ((i - 1) %% 3 == 0) {
    S <- S + 2 * fx[i]
  } else {
    S <- S + 3 * fx[i]
  }
}

I_s38 <- (3 * h / 8) * S

# Bandingkan dengan hasil bawaan R
I_builtin <- integrate(f, lower = a, upper = b)$value

cat("=====================================================\n")
cat(" METODE SIMPSON 3/8\n")
cat("=====================================================\n")
cat(" Jumlah sub-selang (n)        :", n, "\n")
cat(" Hasil Simpson 3/8 Komposit   :", I_s38, "\n")
cat(" Hasil integrate() (built-in) :", I_builtin, "\n")
cat(" Galat absolut                :", abs(I_builtin - I_s38), "\n")
cat("=====================================================\n")
```

**Penjelasan kode**

1. Mendefinisikan fungsi yang akan diintegralkan.
2. Menetapkan batas integrasi `a` dan `b`.
3. Memilih jumlah sub-selang `n` (harus kelipatan 3).
4. Menghitung panjang langkah `h`.
5. Membuat vektor titik $x_i$ dan nilai $f(x_i)$.
6. Inisialisasi jumlah `S` dengan $f(x_0)+f(x_n)$.
7. Mengisi koefisien: jika $(i-1) \bmod 3 = 0$ maka koefisien 2, selain itu koefisien 3 (indeks R dimulai dari 1, sehingga `i - 1` adalah indeks matematis).
8. Menghitung aproksimasi $(3h/8)\,S$.
9. Membandingkan dengan fungsi bawaan `integrate()`.

> Simpson 3/8 memiliki orde galat $O(h^4)$: semakin kecil $h$ (semakin besar $n$), semakin akurat hasilnya.

#### Jawaban Contoh 1

| Besaran | Nilai |
|---|---|
| Simpson 3/8 komposit ($n=300$) | 27.2769561342 |
| Nilai eksak (`integrate()`) | 27.2769561321 |
| Galat absolut | $\approx 2.09\times10^{-9}$ |

**Analisis.** Dengan $n=300$ (atau $h=0.01$), Simpson 3/8 menghasilkan nilai yang cocok dengan nilai eksak hingga sekitar sembilan angka signifikan, dengan galat absolut sekitar $2\times10^{-9}$. Ini sesuai dengan sifat orde galat $O(h^4)$: dengan $h=0.01$, galat berada pada kisaran $10^{-8}$ hingga $10^{-9}$ dikalikan konstanta yang bergantung pada turunan keempat fungsi. Fungsi $e^{-x}\sin(x^2)$ berosilasi makin cepat ketika $x$ membesar sehingga turunan keempatnya cukup besar di ujung kanan interval, tetapi suku $3x^2$ (polinomial derajat 2) diintegralkan secara eksak oleh Simpson 3/8. Jadi galat sepenuhnya berasal dari komponen osilatif, dan $n=300$ sudah lebih dari cukup untuk keperluan praktis.

---

### 3.3 Algoritma Boole Komposit

1. Input $f(x)$, batas $a,b$, jumlah sub-selang $n$ (kelipatan 4).
2. Hitung $h=\frac{b-a}{n}$.
3. Bentuk titik $x_i=a+ih$, $i=0,\dots,n$.
4. Hitung $f(x_i)$ untuk semua $i$.
5. Inisialisasi $S\leftarrow 7f(x_0)+7f(x_n)$.
6. Untuk $i=1,\dots,n-1$, dengan $k=i \bmod 4$:
   - $k=1$ atau $k=3$: $S\leftarrow S+32f(x_i)$,
   - $k=2$: $S\leftarrow S+12f(x_i)$,
   - $k=0$: $S\leftarrow S+\mathbf{14}f(x_i)$ ← *langkah ini tidak ada pada modul asli*.
7. $I\leftarrow \frac{2h}{45}S$.

### Contoh 2 — Boole

Hitung

$$I_2=\int_1^5\left(e^{-x}\sin(x^2)+3x^2\right)dx$$

dengan $n=400$ menggunakan Boole komposit.

### Kode R Boole (sudah dikoreksi)

```r
###############################################################
# METODE BOOLE RULE (KOMPOSIT)
# Numerical Integration Part 2
###############################################################

# Definisi fungsi (bisa diganti)
f <- function(x) exp(-x) * sin(x^2) + 3 * x^2

# Batas integrasi
a <- 1
b <- 5

# Jumlah sub-selang (harus kelipatan 4)
n <- 400

# Lebar langkah
h <- (b - a) / n

# Titik x dan nilai f(x)
x  <- numeric(n + 1)
fx <- numeric(n + 1)

for (i in 0:n) {
  x[i + 1]  <- a + i * h
  fx[i + 1] <- f(x[i + 1])
}

# Rumus Boole Komposit
S <- 7 * fx[1] + 7 * fx[n + 1]

for (i in 2:n) {
  k <- (i - 1) %% 4
  if (k == 1 || k == 3) {
    S <- S + 32 * fx[i]
  } else if (k == 2) {
    S <- S + 12 * fx[i]
  } else {                      # k == 0 : titik sambungan antarpanel
    S <- S + 14 * fx[i]         # KOREKSI: 7 + 7 = 14 (tidak ada di modul asli)
  }
}

I_boole <- (2 * h / 45) * S

# Bandingkan dengan hasil bawaan R
I_builtin <- integrate(f, lower = a, upper = b)$value

cat("=====================================================\n")
cat(" METODE BOOLE\n")
cat("=====================================================\n")
cat(" Jumlah sub-selang (n)        :", n, "\n")
cat(" Hasil Boole Komposit         :", I_boole, "\n")
cat(" Hasil integrate() (built-in) :", I_builtin, "\n")
cat(" Galat absolut                :", abs(I_builtin - I_boole), "\n")
cat("=====================================================\n")
```

**Penjelasan kode**

1. Mendefinisikan fungsi $f(x)$.
2. Menentukan batas integrasi.
3. Menentukan jumlah sub-selang (harus kelipatan 4).
4. Menghitung panjang langkah `h`.
5. Menghasilkan titik $x_i$ dan nilai $f(x_i)$.
6. Inisialisasi dengan $7f(x_0)+7f(x_n)$.
7. Mengisi koefisien Boole: posisi ke-1 dan ke-3 mod 4 bernilai 32, posisi ke-2 mod 4 bernilai 12, dan posisi ke-0 mod 4 (titik sambungan antarpanel) bernilai 14.
8. Menghitung nilai akhir $(2h/45)\,S$.
9. Membandingkan dengan hasil bawaan R.

> Boole memiliki orde galat $O(h^6)$, sehingga merupakan salah satu rumus Newton–Cotes paling akurat sebelum rentan terhadap osilasi (fenomena Runge) pada orde yang lebih tinggi.

#### Jawaban Contoh 2

| Versi kode | Hasil ($n=400$) | Galat absolut |
|---|---|---|
| Kode modul asli (tanpa koefisien 14) | 105.0546913703 | 19.0643 |
| **Kode terkoreksi** | **124.1189547187** | $\approx 1.4\times10^{-12}$ |
| Nilai eksak (`integrate()`) | 124.1189547187 | — |

**Analisis.** Hasil Boole komposit yang benar dengan $n=400$ mencapai galat sekitar $10^{-12}$, jauh lebih kecil dibanding Simpson 3/8 pada Contoh 1 meskipun $n$ dan $h$ hampir sebanding ($h=0.01$ pada kedua kasus). Hal ini sejalan dengan orde galat $O(h^6)$ untuk Boole versus $O(h^4)$ untuk Simpson 3/8. Perhatikan pula bahwa kode pada modul asli menghasilkan galat 19.06, atau sekitar 15% dari nilai sebenarnya. Penyebabnya adalah titik-titik sambungan antarpanel (indeks kelipatan 4) tidak diberi bobot. Padahal titik tersebut merupakan ujung kanan satu panel sekaligus ujung kiri panel berikutnya, sehingga bobotnya harus $7+7=14$. Pelajaran praktisnya: setiap implementasi metode komposit sebaiknya selalu diuji terhadap nilai eksak atau nilai `integrate()`, karena galat sebesar ini menunjukkan kesalahan implementasi, bukan galat pemotongan metode.

---

### 3.4 Algoritma Romberg

1. Input $f(x)$, batas $a,b$, maksimum iterasi $K$.
2. Bentuk matriks $R$ berukuran $K\times K$.
3. Untuk $k=1,\dots,K$: $n\leftarrow 2^{k-1}$, $h\leftarrow\frac{b-a}{n}$, lalu $R_{k,1}\leftarrow\frac h2\left[f(x_0)+2\sum_{i=1}^{n-1}f(x_i)+f(x_n)\right]$.
4. Untuk $j=2,\dots,K$ dan $k=j,\dots,K$: $R_{k,j}\leftarrow R_{k,j-1}+\dfrac{R_{k,j-1}-R_{k-1,j-1}}{4^{j-1}-1}$.
5. Kembalikan $R$ sebagai tabel Romberg.

### Contoh 3 — Romberg

Hitung

$$I_3=\int_0^{\pi/2}\left(e^{-x}\sin(x^2)+3x^2\right)dx$$

dan amati bagaimana nilai pada tabel Romberg mendekati nilai eksak.

### Kode R Romberg (sudah dikoreksi)

```r
###############################################################
# METODE ROMBERG INTEGRATION
# Numerical Integration Part 2 - Komputasi Statistik
###############################################################

# Definisi fungsi (bisa diganti)
f <- function(x) exp(-x) * sin(x^2) + 3 * x^2

# Batas integrasi
a <- 0
b <- pi / 2

# Maksimum level iterasi
max_k <- 5

# Matriks kosong untuk tabel Romberg
R <- matrix(0, nrow = max_k, ncol = max_k)

# Langkah 1: Aturan Trapesium untuk setiap level
for (k in 1:max_k) {
  n  <- 2^(k - 1)
  h  <- (b - a) / n
  x  <- seq(a, b, by = h)
  fx <- f(x)
  # KOREKSI: untuk n = 1 tidak ada titik dalam, sehingga jumlahnya 0
  inner <- if (n > 1) sum(fx[2:n]) else 0
  R[k, 1] <- (h / 2) * (fx[1] + 2 * inner + fx[n + 1])
}

# Langkah 2: Richardson Extrapolation
for (j in 2:max_k) {
  for (k in j:max_k) {
    R[k, j] <- R[k, j - 1] +
      (R[k, j - 1] - R[k - 1, j - 1]) / (4^(j - 1) - 1)
  }
}

colnames(R) <- paste0("R", 1:max_k)
rownames(R) <- paste0("Iter-", 1:max_k)

print(R)

# Bandingkan dengan hasil bawaan R
I_builtin <- integrate(f, lower = a, upper = b)$value
I_romberg <- R[max_k, max_k]

cat("=====================================================\n")
cat(" METODE ROMBERG - HASIL\n")
cat("=====================================================\n")
cat(" Level Iterasi (max_k)        :", max_k, "\n")
cat(" Hasil Romberg Integration    :", I_romberg, "\n")
cat(" Hasil integrate() (built-in) :", I_builtin, "\n")
cat(" Galat absolut                :", abs(I_builtin - I_romberg), "\n")
cat("=====================================================\n")
```

**Penjelasan kode**

1. Mendefinisikan fungsi yang akan diintegralkan.
2. Menentukan batas integrasi $a,b$.
3. Menentukan jumlah iterasi maksimum Romberg.
4. Membuat tabel kosong `R`.
5. Mengisi kolom pertama $R_{k,1}$ dengan aturan trapesium.
6. Melakukan Richardson Extrapolation untuk menghasilkan $R_{k,j}$.
7. Memberi nama kolom dan baris tabel Romberg.
8. Mengambil hasil akhir $R_{K,K}$.

> Romberg adalah metode berorde tinggi (konvergensi sangat cepat) yang menggabungkan trapesium dan ekstrapolasi, sehingga galat turun sangat cepat.

#### Jawaban Contoh 3

Tabel Romberg ($K=5$):

| | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| Iter-1 | 5.915600 | | | | |
| Iter-2 | 4.618365 | 4.185953 | | | |
| Iter-3 | 4.285568 | 4.174635 | 4.173881 | | |
| Iter-4 | 4.201264 | 4.173163 | 4.173065 | 4.173052 | |
| Iter-5 | 4.180119 | 4.173071 | 4.173065 | 4.173065 | **4.173064788** |

| Besaran | Nilai |
|---|---|
| Romberg $R_{5,5}$ | 4.1730647882 |
| Nilai eksak | 4.1730647835 |
| Galat absolut | $\approx 4.6\times10^{-9}$ |

**Analisis.** Kolom pertama (trapesium murni) konvergen sangat lambat: dari 5.9156 pada satu panel menjadi 4.1801 pada 16 panel, masih menyimpang sekitar 0.007 dari nilai eksak. Setiap kolom ekstrapolasi berikutnya mempercepat konvergensi secara dramatis. Pada baris terakhir, galat turun dari sekitar $7\times10^{-3}$ (kolom 1) menjadi sekitar $6\times10^{-6}$ (kolom 2), lalu sekitar $5\times10^{-8}$ pada kolom 3–4 dan $5\times10^{-9}$ pada kolom 5, padahal seluruh baris itu memakai titik evaluasi yang sama (hanya 17 titik). Ini menunjukkan keunggulan Richardson Extrapolation: akurasi tinggi diperoleh tanpa tambahan evaluasi fungsi. Ada satu hal menarik: diagonal tidak selalu menurun monoton. Misalnya $R_{4,4}=4.173052$ (galat $\approx1.3\times10^{-5}$) justru lebih jauh dari $R_{4,3}=4.173065$ (galat $\approx1.7\times10^{-7}$). Ini terjadi karena ekstrapolasi mengandaikan galat trapesium berbentuk deret pangkat $h^2, h^4, h^6,\dots$ yang rapi, sedangkan integran berosilasi belum berada pada rezim asimtotik pada $h$ yang masih besar. Pada level yang lebih tinggi (5 ke atas), pola ini teratasi dan diagonal konvergen stabil.

---

## 4. Analisis Konvergensi

Tujuan: membandingkan jumlah sub-selang $n$ yang diperlukan Simpson 3/8, Boole, dan Romberg agar mendekati nilai eksak. Fungsi uji: $f(x)=e^{-x}\sin(x^2)+3x^2$ pada $[0,3]$, nilai eksak $27.276956132089$.

| Simpson 3/8 ($n$ kelipatan 3) | Galat absolut | | Boole ($n$ kelipatan 4) | Galat absolut | | Romberg (level $k$, $n=2^{k-1}$) | Galat $R_{k,k}$ |
|---|---|---|---|---|---|---|---|
| 12 | $1.2\times10^{-3}$ | | 8 | $5.2\times10^{-3}$ | | $k=4$ ($n=8$) | $5.9\times10^{-3}$ |
| 24 | $5.6\times10^{-5}$ | | 16 | $5.7\times10^{-5}$ | | $k=5$ ($n=16$) | $1.7\times10^{-4}$ |
| 48 | $3.3\times10^{-6}$ | | 32 | $6.1\times10^{-7}$ | | $k=6$ ($n=32$) | $1.0\times10^{-6}$ |
| 96 | $2.0\times10^{-7}$ | | 64 | $8.4\times10^{-9}$ | | $k=7$ ($n=64$) | $1.0\times10^{-9}$ |
| 192 | $1.2\times10^{-8}$ | | 128 | $1.3\times10^{-10}$ | | $k=8$ ($n=128$) | $3.0\times10^{-13}$ |
| 384 | $7.8\times10^{-10}$ | | 256 | $2.0\times10^{-12}$ | | $k=9$ ($n=256$) | $3.6\times10^{-15}$ |
| 768 | $4.9\times10^{-11}$ | | 512 | $3.6\times10^{-15}$ | | | |

Jumlah sub-selang minimum untuk mencapai galat absolut kurang dari $10^{-9}$ (secara kasar):

| Metode | $n$ yang dibutuhkan |
|---|---|
| Simpson 3/8 | ≈ 384–768 |
| Boole | ≈ 128 |
| Romberg | ≈ 64–128 ($k=7$ hampir mencapai $10^{-9}$, $k=8$ jelas di bawahnya) |

**Analisis.** Ketiga metode sama-sama konvergen ke nilai eksak, tetapi dengan kecepatan yang sangat berbeda. Pada Simpson 3/8, setiap penggandaan $n$ mengurangi galat sekitar 16 kali ($2^4$), sesuai orde $O(h^4)$. Pada Boole, penggandaan $n$ mengurangi galat sekitar 60–65 kali ($2^6$), sesuai orde $O(h^6)$. Romberg konvergen paling cepat pada $n$ yang besar. Dari $k=6$ ke $k=8$, galatnya turun dari $10^{-6}$ ke $10^{-13}$, karena setiap level menambah satu orde ketelitian ($h^2\to h^4\to h^6\to\cdots$). Namun, pada $n$ kecil (misalnya $n\le 16$) Romberg belum tentu lebih baik dari Boole atau Simpson 3/8, sebab ekstrapolasi baru efektif setelah galat trapesium memasuki rezim asimtotik. Dalam hal efisiensi, Boole dan Romberg membutuhkan sekitar 3–6 kali lebih sedikit evaluasi fungsi daripada Simpson 3/8 untuk mencapai akurasi $10^{-9}$. Perlu dicatat juga bahwa galat tidak dapat turun tanpa batas: pada $n$ sangat besar galat berhenti di kisaran $10^{-15}$, yaitu batas presisi bilangan *floating point* (galat pembulatan), sehingga memperbesar $n$ lebih jauh tidak lagi bermanfaat.

---

## 5. Latihan

### Latihan 1

Gunakan ketiga metode (Simpson 3/8, Boole, Romberg) untuk menghitung

$$\int_0^{2\pi}(\sin x+e^{-x})\,dx.$$

Bandingkan hasil dan galat relatif masing-masing metode.

**Nilai eksak (analitik):** $\int_0^{2\pi}\sin x\,dx=0$ dan $\int_0^{2\pi}e^{-x}dx=1-e^{-2\pi}$, sehingga $I=1-e^{-2\pi}=0.998132557268\ldots$

**Kode R (fungsi pembantu yang dipakai ulang pada latihan dan tugas):**

```r
simpson38 <- function(f, a, b, n) {
  stopifnot(n %% 3 == 0)
  h <- (b - a) / n; i <- 0:n; fx <- f(a + i * h)
  w <- rep(3, n + 1); w[1] <- 1; w[n + 1] <- 1
  w[i %% 3 == 0 & i > 0 & i < n] <- 2
  (3 * h / 8) * sum(w * fx)
}

boole <- function(f, a, b, n) {
  stopifnot(n %% 4 == 0)
  h <- (b - a) / n; i <- 0:n; fx <- f(a + i * h)
  w <- numeric(n + 1)
  w[i %% 4 == 1 | i %% 4 == 3] <- 32
  w[i %% 4 == 2] <- 12
  w[i %% 4 == 0] <- 14
  w[1] <- 7; w[n + 1] <- 7
  (2 * h / 45) * sum(w * fx)
}

romberg <- function(f, a, b, K) {
  R <- matrix(0, K, K)
  for (k in 1:K) {
    n <- 2^(k - 1); h <- (b - a) / n; fx <- f(seq(a, b, by = h))
    inner <- if (n > 1) sum(fx[2:n]) else 0
    R[k, 1] <- (h / 2) * (fx[1] + 2 * inner + fx[n + 1])
  }
  for (j in 2:K) for (k in j:K)
    R[k, j] <- R[k, j - 1] + (R[k, j - 1] - R[k - 1, j - 1]) / (4^(j - 1) - 1)
  R
}

g <- function(x) sin(x) + exp(-x)
a <- 0; b <- 2 * pi
eksak <- 1 - exp(-2 * pi)

I_s <- simpson38(g, a, b, 48)
I_b <- boole(g, a, b, 48)
I_r <- romberg(g, a, b, 6)[6, 6]

hasil <- data.frame(
  Metode = c("Simpson 3/8 (n=48)", "Boole (n=48)", "Romberg (K=6)"),
  Aproksimasi = c(I_s, I_b, I_r),
  Eksak = eksak,
  Galat_Relatif = abs(c(I_s, I_b, I_r) - eksak) / abs(eksak)
)
print(hasil, digits = 12)
```

**Jawaban:**

| Metode | Aproksimasi | Nilai eksak | Galat absolut | Galat relatif |
|---|---|---|---|---|
| Simpson 3/8 ($n=48$) | 0.998136205515 | 0.998132557268 | $3.65\times10^{-6}$ | $3.66\times10^{-6}$ |
| Boole ($n=48$) | 0.998132567801 | 0.998132557268 | $1.05\times10^{-8}$ | $1.06\times10^{-8}$ |
| Romberg ($K=6$, $n=32$) | 0.998132557954 | 0.998132557268 | $6.86\times10^{-10}$ | $6.87\times10^{-10}$ |

Tabel Romberg ($K=6$), diagonal: 3.147459, 1.230167, 1.010284, 0.998315, 0.998133261617, 0.998132557954.

**Analisis.** Ketiganya menghasilkan nilai yang sangat dekat dengan $1-e^{-2\pi}\approx0.99813$, tetapi tingkat ketelitiannya berbeda jauh. Dengan jumlah sub-selang yang sama ($n=48$), Boole memiliki galat relatif sekitar $10^{-8}$, yaitu hampir 350 kali lebih kecil daripada Simpson 3/8 ($\approx 3.7\times10^{-6}$). Romberg dengan $K=6$ hanya memakai $n=32$ sub-selang (jadi lebih sedikit evaluasi fungsi daripada dua metode lainnya), tetapi galat relatifnya paling kecil, sekitar $7\times10^{-10}$. Urutan akurasi ini konsisten dengan teori: orde $h^4$ untuk Simpson 3/8, $h^6$ untuk Boole, dan orde yang terus meningkat pada Romberg. Integran ini periodik pada komponen $\sin x$ (satu periode penuh pada $[0,2\pi]$), sehingga aturan trapesium sendiri sudah sangat baik untuk komponen tersebut, dan hal ini turut membantu Romberg. Untuk perbandingan yang adil, perlu diperhatikan bahwa nilai-nilai di atas bergantung pada pilihan $n$: dengan $n$ yang lebih besar, ketiga galat akan mengecil mengikuti pola pada Latihan 2.

### Latihan 2

Untuk Simpson 3/8 dan Boole, buat grafik hubungan antara $n$ dan galat absolut (gunakan beberapa nilai $n$ yang berbeda). Fungsi yang dipakai: integral pada Latihan 1.

**Kode R:**

```r
n_s <- c(6, 12, 24, 48, 96, 192, 384, 768, 1536)   # kelipatan 3
n_b <- c(4, 8, 16, 32, 64, 128, 256, 512)          # kelipatan 4

err_s <- sapply(n_s, function(n) abs(simpson38(g, a, b, n) - eksak))
err_b <- sapply(n_b, function(n) abs(boole(g, a, b, n) - eksak))

plot(n_s, err_s, log = "xy", type = "b", pch = 16, col = "blue",
     xlab = "n (jumlah sub-selang)", ylab = "Galat absolut",
     main = "Konvergensi Simpson 3/8 vs Boole",
     xlim = range(c(n_s, n_b)), ylim = range(c(err_s, err_b)))
lines(n_b, err_b, type = "b", pch = 17, col = "red")
legend("bottomleft", c("Simpson 3/8", "Boole"),
       col = c("blue", "red"), pch = c(16, 17), lty = 1)

# Estimasi orde konvergensi (kemiringan log-log)
coef(lm(log(err_s) ~ log(n_s)))[2]   # kira-kira -4
coef(lm(log(err_b[err_b > 1e-13]) ~ log(n_b[err_b > 1e-13])))[2]  # kira-kira -6
```

**Jawaban (data untuk grafik):**

| Simpson 3/8: $n$ | Galat absolut | | Boole: $n$ | Galat absolut |
|---|---|---|---|---|
| 6 | $1.19\times10^{-2}$ | | 4 | $1.22\times10^{-2}$ |
| 12 | $8.80\times10^{-4}$ | | 8 | $3.69\times10^{-4}$ |
| 24 | $5.77\times10^{-5}$ | | 16 | $7.16\times10^{-6}$ |
| 48 | $3.65\times10^{-6}$ | | 32 | $1.19\times10^{-7}$ |
| 96 | $2.29\times10^{-7}$ | | 64 | $1.88\times10^{-9}$ |
| 192 | $1.43\times10^{-8}$ | | 128 | $2.95\times10^{-11}$ |
| 384 | $8.94\times10^{-10}$ | | 256 | $4.60\times10^{-13}$ |
| 768 | $5.59\times10^{-11}$ | | 512 | $3.66\times10^{-15}$ |
| 1536 | $3.50\times10^{-12}$ | | | |

Pada skala log-log, kedua kurva berupa garis lurus menurun. Rasio galat antar-penggandaan $n$: Simpson 3/8 ≈ 16 ($2^4$), Boole ≈ 64 ($2^6$).

**Analisis.** Grafik log–log antara $n$ dan galat absolut menunjukkan dua garis lurus dengan kemiringan berbeda. Kemiringan Simpson 3/8 sekitar $-4$, sedangkan Boole sekitar $-6$, yang secara empiris mengonfirmasi orde konvergensi $O(h^4)$ dan $O(h^6)$. Karena kemiringan Boole lebih curam, kurva Boole memotong dan kemudian jauh melampaui Simpson 3/8 seiring bertambahnya $n$. Pada $n$ yang sangat kecil (sekitar 4–6) kedua metode memiliki galat sebanding, sehingga keunggulan orde tinggi baru terasa setelah $h$ cukup kecil. Pada $n=512$, Boole sudah mencapai galat $\approx 4\times10^{-15}$, yakni mendekati batas presisi mesin (*machine epsilon*), sehingga titik-titik berikutnya tidak lagi mengikuti garis lurus karena didominasi galat pembulatan. Secara praktis, untuk mencapai galat $10^{-9}$ Boole cukup dengan $n\approx64$–$128$, sementara Simpson 3/8 memerlukan $n\approx384$ atau lebih.

---

## 6. Tugas Praktikum

Definisikan fungsi

$$f(x)=\cos(x^2)+e^{-x}\ln(x+1).$$

Hitung:

- $I_1=\int_0^3 f(x)\,dx$ (Simpson 3/8),
- $I_2=\int_0^4 f(x)\,dx$ (Boole),
- $I_3=\int_0^2 f(x)\,dx$ (Romberg dengan $\max_k=6$).

Bandingkan hasil numerik dengan `integrate()`, lalu buat tabel nilai aproksimasi, nilai eksak, dan galat relatif untuk setiap metode.

**Kode R:**

```r
f <- function(x) cos(x^2) + exp(-x) * log(x + 1)

# I1: Simpson 3/8, n = 300 (kelipatan 3)
I1 <- simpson38(f, 0, 3, 300);  E1 <- integrate(f, 0, 3)$value
# I2: Boole, n = 400 (kelipatan 4)
I2 <- boole(f, 0, 4, 400);      E2 <- integrate(f, 0, 4)$value
# I3: Romberg, max_k = 6
R3 <- romberg(f, 0, 2, 6);      I3 <- R3[6, 6]; E3 <- integrate(f, 0, 2)$value
print(R3, digits = 8)

tabel <- data.frame(
  Integral = c("I1 (Simpson 3/8)", "I2 (Boole)", "I3 (Romberg)"),
  Aproksimasi = c(I1, I2, I3),
  Eksak = c(E1, E2, E3)
)
tabel$Galat_Relatif <- abs(tabel$Aproksimasi - tabel$Eksak) / abs(tabel$Eksak)
print(tabel, digits = 12)
```

**Jawaban (parameter: $n=300$ untuk Simpson 3/8, $n=400$ untuk Boole, $K=6$ untuk Romberg):**

| Integral | Metode | Aproksimasi | Nilai eksak (`integrate`) | Galat absolut | Galat relatif |
|---|---|---|---|---|---|
| $I_1=\int_0^3 f\,dx$ | Simpson 3/8 ($n=300$) | 1.219918057185 | 1.219918042939 | $1.42\times10^{-8}$ | $1.17\times10^{-8}$ |
| $I_2=\int_0^4 f\,dx$ | Boole ($n=400$) | 1.158208415162 | 1.158208415164 | $1.35\times10^{-12}$ | $1.17\times10^{-12}$ |
| $I_3=\int_0^2 f\,dx$ | Romberg ($K=6$) | 0.873658628423 | 0.873658642283 | $1.39\times10^{-8}$ | $1.59\times10^{-8}$ |

Tabel Romberg untuk $I_3$ ($K=6$):

| | R1 | R2 | R3 | R4 | R5 | R6 |
|---|---|---|---|---|---|---|
| Iter-1 | 0.495037 | | | | | |
| Iter-2 | 1.042816 | 1.225408 | | | | |
| Iter-3 | 0.916967 | 0.875017 | 0.851658 | | | |
| Iter-4 | 0.883895 | 0.872872 | 0.872729 | 0.873063 | | |
| Iter-5 | 0.876177 | 0.873604 | 0.873653 | 0.873668 | 0.873670 | |
| Iter-6 | 0.874286 | 0.873655 | 0.873659 | 0.873659 | 0.873659 | **0.873658628** |

Sebagai pelengkap, pengaruh $n$ terhadap galat relatif:

| $n$ (Simpson 3/8, $[0,3]$) | Galat relatif | | $n$ (Boole, $[0,4]$) | Galat relatif |
|---|---|---|---|---|
| 30 | $1.36\times10^{-4}$ | | 40 | $1.61\times10^{-5}$ |
| 60 | $7.57\times10^{-6}$ | | 100 | $9.85\times10^{-9}$ |
| 300 | $1.17\times10^{-8}$ | | 400 | $1.17\times10^{-12}$ |
| 600 | $7.29\times10^{-10}$ | | 800 | $1.59\times10^{-14}$ |
| 1200 | $4.56\times10^{-11}$ | | 1600 | $7.67\times10^{-16}$ |

**Analisis.** Ketiga aproksimasi sesuai dengan `integrate()` pada tingkat ketelitian yang tinggi, dengan galat relatif $10^{-8}$ untuk Simpson 3/8 dan Romberg, serta $10^{-12}$ untuk Boole. Perlu diingat bahwa perbandingan ini tidak sepenuhnya setara, karena ketiganya memakai interval dan jumlah titik yang berbeda ($n=300$, $n=400$, dan $2^5=32$ sub-selang). Yang menarik adalah efisiensi Romberg: dengan hanya 33 titik evaluasi fungsi, galat relatifnya ($1.6\times10^{-8}$) setara dengan Simpson 3/8 yang memakai 301 titik. Boole mencapai galat paling kecil karena orde $O(h^6)$ dan $h=0.01$. Fungsi $\cos(x^2)$ berosilasi makin cepat seiring $x$ membesar, dan $\ln(x+1)$ halus pada $[0,4]$, sehingga integrand ini tergolong mulus (analitik) dan cocok untuk metode orde tinggi. Hasil Boole yang buruk pada $n$ kecil ($n=4$ dan $n=8$ memberi galat relatif >60%) menunjukkan bahwa metode orde tinggi tetap membutuhkan $h$ yang cukup kecil terhadap skala osilasi integran. Pada tabel Romberg, nilai diagonal $R_{3,3}=0.8517$ masih berjarak 2.5% dari nilai eksak, lalu turun ke $10^{-5}$ pada $R_{5,5}$ dan $10^{-8}$ pada $R_{6,6}$. Jadi $\max_k=6$ memberi hasil kira-kira delapan angka signifikan. Jika diperlukan ketelitian lebih tinggi, level iterasi dapat dinaikkan (galat $R_{8,8}\approx 7\times10^{-15}$).

---

## Ringkasan Perbandingan Metode

| Aspek | Simpson 3/8 | Boole | Romberg |
|---|---|---|---|
| Jenis | Newton–Cotes derajat 3 | Newton–Cotes derajat 4 | Trapesium + ekstrapolasi Richardson |
| Orde galat | $O(h^4)$ | $O(h^6)$ | Meningkat tiap kolom ($h^2,h^4,h^6,\dots$) |
| Syarat $n$ | Kelipatan 3 | Kelipatan 4 | $n=2^{k-1}$ |
| Pola koefisien | 1,3,3,2,3,3,2,…,3,3,1 | 7,32,12,32,14,32,12,32,14,…,7 | Bobot trapesium, lalu ekstrapolasi |
| Kelebihan | Sederhana, bentuk komposit mudah diimplementasikan | Sangat akurat untuk fungsi mulus | Akurasi tertinggi per evaluasi fungsi, tersedia estimasi galat dari tabel |
| Kelemahan | Konvergensi paling lambat di antara ketiganya | Rentan bila $h$ besar atau fungsi tidak mulus | Memerlukan integran yang cukup mulus |

---

*Modul Praktikum Komputasi Statistik — Pertemuan 12. Program Studi Sains Data, Institut Teknologi Sumatera.*
