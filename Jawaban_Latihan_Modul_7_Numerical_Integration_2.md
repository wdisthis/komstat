<div align="center">

# Latihan Modul 7 Komputasi Statistik

## Numerical Integration 2: Metode Simpson 3/8, Boole, dan Romberg

Program Studi Sains Data, Institut Teknologi Sumatera

</div>

---

## 1. Soal

### 1.1 Instruksi Tugas

Dalam analisis data ilmiah, sering muncul fungsi kompleks yang sulit diintegralkan secara analitik. Anda diminta membandingkan tiga metode integrasi numerik pada beberapa fungsi kompleks, serta membandingkan hasilnya dengan nilai acuan dari fungsi bawaan `integrate()` di R, lalu menganalisis metode mana yang paling baik pada kasus ini.

### 1.2 Parameter yang Digunakan

- Jumlah panel Simpson 3/8 dan Boole: $n = 12$
- Level maksimum Romberg: $K_{\max} = 4$
- Total integral: $3 \text{ fungsi} \times 3 \text{ batas} = 9$
- Total nilai aproksimasi: $9 \text{ integral} \times 3 \text{ metode} = 27$ hasil

### 1.3 Fungsi Kompleks

$$
\begin{aligned}
f_1(x) &= e^{-0.4x}\sin(3x) + x^2 \\
f_2(x) &= x^3 e^{-x} + \sin(x^2) \\
f_3(x) &= e^{-x^2} + x\cos(2x)
\end{aligned}
$$

### 1.4 Batas Integral

$$
[0,\,2], \qquad [0,\,4], \qquad [0,\,\pi]
$$

### 1.5 Tugas Anda

1. Untuk setiap kombinasi (fungsi, batas), hitung integral dengan:
   - Simpson 3/8 ($n = 12$),
   - Boole ($n = 12$),
   - Romberg ($K_{\max} = 4$).
2. Untuk setiap kasus, hitung juga nilai acuan dengan:
   $$I_{\text{exact}} \approx \texttt{integrate(f, lower = a, upper = b)\$value}$$
3. Hitung galat absolut:
   $$\text{Galat} = \left| I_{\text{metode}} - I_{\text{integrate}} \right|$$
4. Isikan semua hasil pada tabel.
5. Berikan analisis singkat (3 sampai 5 kalimat) mengenai:
   - Metode mana yang paling sering memberikan galat terkecil?
   - Apakah ada pola tertentu terkait jenis fungsi atau panjang interval terhadap kinerja masing-masing metode?
   - Jika harus memilih satu metode untuk digunakan pada sistem nyata, metode mana yang Anda pilih dan mengapa?

---

## 2. Landasan Teori Singkat

Misalkan selang $[a,b]$ dibagi menjadi $n$ sub-selang sama panjang dengan $h = \dfrac{b-a}{n}$ dan $x_i = a + ih$.

### 2.1 Simpson 3/8 Komposit ($n$ kelipatan 3)

$$
\int_a^b f(x)\,dx \approx \frac{3h}{8}\left[ f(x_0) + f(x_n) + 3\!\!\sum_{\substack{i=1 \\ i \not\equiv 0 \,(\mathrm{mod}\ 3)}}^{n-1}\!\! f(x_i) + 2\!\!\sum_{\substack{i=3 \\ i \equiv 0 \,(\mathrm{mod}\ 3)}}^{n-3}\!\! f(x_i) \right], \qquad E = \mathcal{O}(h^4)
$$

### 2.2 Boole Komposit ($n$ kelipatan 4)

Satu panel:

$$
\int_{x_0}^{x_4} f(x)\,dx \approx \frac{2h}{45}\left[ 7f(x_0) + 32f(x_1) + 12f(x_2) + 32f(x_3) + 7f(x_4) \right]
$$

Bentuk komposit: bobot $7$ di kedua ujung, $32$ untuk $i \equiv 1,3 \pmod 4$, $12$ untuk $i \equiv 2 \pmod 4$, dan $14 = 7 + 7$ untuk titik sambungan antar-panel ($i \equiv 0 \pmod 4$, $0 < i < n$).

$$
E = \mathcal{O}(h^6)
$$

### 2.3 Romberg

Kolom pertama adalah aturan trapesium komposit dengan $2^{k-1}$ sub-selang:

$$
R_{k,1} = \frac{h_k}{2}\left[ f(a) + 2\sum_{i=1}^{2^{k-1}-1} f(a + ih_k) + f(b) \right], \qquad h_k = \frac{b-a}{2^{k-1}}
$$

Kolom berikutnya memakai ekstrapolasi Richardson:

$$
R_{k,j} = R_{k,j-1} + \frac{R_{k,j-1} - R_{k-1,j-1}}{4^{\,j-1} - 1}, \qquad j = 2,\dots,k
$$

Hasil akhir adalah $R_{K,K}$.

---

## 3. Catatan Penting

Dua penyesuaian dilakukan terhadap kode di modul praktikum agar hasilnya benar:

1. **Boole komposit.** Kode modul hanya memberi bobot $32$ dan $12$ pada titik interior, sehingga titik sambungan antar-panel ($i$ kelipatan 4) tidak mendapat bobot sama sekali. Titik tersebut dipakai oleh dua panel sehingga bobotnya $7+7=14$. Tanpa koreksi ini galat Boole sangat besar (antara $0.03$ dan $2.3$).
2. **Romberg level $k=1$.** Pada $k=1$ nilai $n=1$, sehingga `sum(fx[2:n])` di R menjadi `sum(fx[2:1])`, yaitu urutan menurun `c(2, 1)` yang menjumlahkan dua titik ujung. Ini salah. Dipakai `if (n > 1) sum(fx[2:n]) else 0`. Titik $x$ juga dibuat dengan `a + (0:n) * h` agar jumlah titik tidak bergeser akibat galat floating point pada `seq()`.

Nilai pada tabel dihitung dengan algoritma yang sama persis dengan kode R di bawah, dengan acuan QUADPACK (basis `integrate()` di R). Menjalankan kode R di bawah seharusnya memberi angka yang sama sampai sekitar 8 digit; selisih pada digit terakhir kolom $I_{\text{integrate}}$ bisa terjadi karena toleransi bawaan R lebih longgar.

---

## 4. Jawaban: Kode R dan Analisis per Chunk

### Chunk 1: Definisi Fungsi, Batas, dan Parameter

```r
# ============================================================
# CHUNK 1: Definisi fungsi, batas integral, dan parameter
# ============================================================

f1 <- function(x) exp(-0.4 * x) * sin(3 * x) + x^2
f2 <- function(x) x^3 * exp(-x) + sin(x^2)
f3 <- function(x) exp(-x^2) + x * cos(2 * x)

fungsi <- list(f1 = f1, f2 = f2, f3 = f3)

batas <- list(
  "[0, 2]"  = c(0, 2),
  "[0, 4]"  = c(0, 4),
  "[0, pi]" = c(0, pi)
)

n_panel <- 12   # Simpson 3/8 (kelipatan 3) dan Boole (kelipatan 4)
K_max   <- 4    # Level maksimum Romberg
```

**Analisis.** Ketiga fungsi disimpan dalam satu `list` dan batas dalam list lain, sehingga 9 kombinasi (fungsi, batas) diproses dengan loop tanpa menulis ulang kode. Nilai $n = 12$ habis dibagi $3$ (syarat Simpson 3/8) dan $4$ (syarat Boole), jadi satu nilai $n$ cukup untuk kedua metode. Romberg tidak memakai $n$ tetapi $K_{\max}=4$, yang berarti $n_{\text{akhir}} = 2^{4-1} = 8$ sub-selang, atau $9$ titik evaluasi. Jadi Romberg di sini memakai titik lebih sedikit daripada Simpson 3/8 dan Boole ($13$ titik), hal yang perlu diingat saat membaca hasil.

---

### Chunk 2: Simpson 3/8 Komposit

```r
# ============================================================
# CHUNK 2: Simpson 3/8 komposit
# ============================================================

simpson38 <- function(f, a, b, n) {
  if (n %% 3 != 0) stop("n harus kelipatan 3")
  h  <- (b - a) / n
  x  <- a + (0:n) * h
  fx <- f(x)

  S <- fx[1] + fx[n + 1]
  for (i in 1:(n - 1)) {
    if (i %% 3 == 0) {
      S <- S + 2 * fx[i + 1]   # titik sambungan antar-panel
    } else {
      S <- S + 3 * fx[i + 1]
    }
  }
  (3 * h / 8) * S
}
```

**Analisis.** Kode mengikuti rumus pada bagian 2.1. Indeks R mulai dari 1, sehingga $f(x_i)$ diambil dari `fx[i + 1]`. Titik interior dengan $i$ kelipatan 3 adalah titik pertemuan dua panel (dipakai dua kali), sehingga bobotnya $2$ dan bukan $3$. Pengecekan `n %% 3` mencegah pemakaian $n$ yang salah. Galat berorde $\mathcal{O}(h^4)$; untuk $n=12$ pada $[0,4]$ lebar langkahnya $h = 1/3$.

---

### Chunk 3: Boole Komposit

```r
# ============================================================
# CHUNK 3: Boole komposit
# ============================================================

boole <- function(f, a, b, n) {
  if (n %% 4 != 0) stop("n harus kelipatan 4")
  h  <- (b - a) / n
  x  <- a + (0:n) * h
  fx <- f(x)

  S <- 7 * fx[1] + 7 * fx[n + 1]
  for (i in 1:(n - 1)) {
    k <- i %% 4
    if (k == 0) {
      S <- S + 14 * fx[i + 1]  # sambungan antar-panel: 7 + 7
    } else if (k == 2) {
      S <- S + 12 * fx[i + 1]
    } else {
      S <- S + 32 * fx[i + 1]  # k = 1 atau 3
    }
  }
  (2 * h / 45) * S
}
```

**Analisis.** Satu panel Boole memakai 5 titik dengan bobot $7, 32, 12, 32, 7$. Saat panel disambung, bobot $7$ di ujung kanan panel sebelumnya bertemu bobot $7$ di ujung kiri panel berikutnya, sehingga titik sambungan berbobot $14$. Cabang `k == 0` inilah koreksi terhadap kode modul. Dengan $n=12$ ada 3 panel, dan titik sambungannya adalah $i=4$ dan $i=8$. Galat berorde $\mathcal{O}(h^6)$, lebih kecil dari Simpson 3/8 ($\mathcal{O}(h^4)$) bila fungsinya cukup mulus pada lebar langkah tersebut.

---

### Chunk 4: Romberg

```r
# ============================================================
# CHUNK 4: Romberg (trapesium komposit + ekstrapolasi Richardson)
# ============================================================

romberg <- function(f, a, b, K) {
  R <- matrix(0, nrow = K, ncol = K)

  # Kolom 1: trapesium komposit dengan n = 2^(k-1) sub-selang
  for (k in 1:K) {
    n  <- 2^(k - 1)
    h  <- (b - a) / n
    x  <- a + (0:n) * h
    fx <- f(x)
    R[k, 1] <- (h / 2) * (fx[1] + 2 * (if (n > 1) sum(fx[2:n]) else 0) + fx[n + 1])
  }

  # Kolom 2..K: ekstrapolasi Richardson
  for (j in 2:K) {
    for (k in j:K) {
      R[k, j] <- R[k, j - 1] +
        (R[k, j - 1] - R[k - 1, j - 1]) / (4^(j - 1) - 1)
    }
  }
  R
}
```

**Analisis.** Kolom pertama adalah aturan trapesium dengan $1, 2, 4, 8$ sub-selang. Kolom berikutnya adalah ekstrapolasi Richardson yang menghilangkan suku galat dominan secara berurutan: kolom 2 setara Simpson 1/3, kolom 3 setara Boole, dan kolom 4 berorde lebih tinggi lagi. Hasil akhir diambil dari elemen diagonal `R[K, K]`. Penambahan `if (n > 1)` memperbaiki kasus $k=1$ yang pada kode asli menghasilkan $R_{1,1}$ yang salah. Setiap level hanya menambah evaluasi fungsi, sehingga totalnya $9$ titik unik untuk $K=4$.

---

### Chunk 5: Menghitung 27 Hasil dan Galat

```r
# ============================================================
# CHUNK 5: Hitung semua kombinasi (3 fungsi x 3 batas x 3 metode)
# ============================================================

hasil <- data.frame()
no <- 1

for (nf in names(fungsi)) {
  f <- fungsi[[nf]]
  for (nb in names(batas)) {
    a <- batas[[nb]][1]
    b <- batas[[nb]][2]

    I_ref <- integrate(f, lower = a, upper = b)$value

    I_s <- simpson38(f, a, b, n_panel)
    I_b <- boole(f, a, b, n_panel)
    I_r <- romberg(f, a, b, K_max)[K_max, K_max]

    metode <- c("Simpson 3/8", "Boole", "Romberg")
    nilai  <- c(I_s, I_b, I_r)

    for (m in 1:3) {
      hasil <- rbind(hasil, data.frame(
        No          = no,
        Fungsi      = nf,
        Batas       = nb,
        Metode      = metode[m],
        I_metode    = nilai[m],
        I_integrate = I_ref,
        Galat       = abs(nilai[m] - I_ref)
      ))
      no <- no + 1
    }
  }
}
```

**Analisis.** Tiga loop bersarang (fungsi, batas, metode) menghasilkan tepat $27$ baris dalam satu `data.frame`. Nilai acuan `I_ref` dihitung sekali per kombinasi (fungsi, batas) lalu dipakai ketiga metode, sehingga perbandingan galat memakai acuan yang identik. Galat dihitung langsung sebagai $|I_{\text{metode}} - I_{\text{integrate}}|$ sesuai instruksi nomor 3. Untuk Romberg yang diambil adalah `[K_max, K_max]`, pojok kanan bawah tabel.

---

### Chunk 6: Menampilkan Tabel dan Rekap Metode Terbaik

```r
# ============================================================
# CHUNK 6: Tampilkan tabel & rekap metode dengan galat terkecil
# ============================================================

options(digits = 10)
print(hasil, row.names = FALSE)

# Metode dengan galat terkecil di tiap kasus
terbaik <- aggregate(Galat ~ Fungsi + Batas, data = hasil, FUN = min)
terbaik <- merge(terbaik, hasil, by = c("Fungsi", "Batas", "Galat"))
print(terbaik[, c("Fungsi", "Batas", "Metode", "Galat")], row.names = FALSE)

# Jumlah kemenangan tiap metode
print(table(terbaik$Metode))
```

**Analisis.** `print(hasil)` menampilkan tabel lengkap yang disalin ke bagian 5. Blok `aggregate` dan `merge` mengambil, untuk tiap kasus, metode dengan galat terkecil, lalu `table()` menghitung berapa kali tiap metode menang. Rekap ini menjawab pertanyaan pertama pada analisis: metode mana yang paling sering memberi galat terkecil.

---

## 5. Tabel Hasil (Perbandingan dengan `integrate()` dan Galat)

Galat terkecil pada setiap kasus dicetak tebal.

| No | Fungsi | Batas | Metode | $I_{\text{metode}}$ | $I_{\text{integrate}}$ | Galat |
|:--:|:------:|:-----:|:-------|--------------------:|-----------------------:|------:|
| 1 | $f_1$ | $[0,2]$ | Simpson 3/8 | 2.8584947052 | 2.8583612987 | $1.334\times10^{-4}$ |
| 2 | $f_1$ | $[0,2]$ | Boole | 2.8583566416 | 2.8583612987 | $\mathbf{4.657\times10^{-6}}$ |
| 3 | $f_1$ | $[0,2]$ | Romberg | 2.8580912362 | 2.8583612987 | $2.701\times10^{-4}$ |
| 4 | $f_1$ | $[0,4]$ | Simpson 3/8 | 21.6135870446 | 21.6097764942 | $3.811\times10^{-3}$ |
| 5 | $f_1$ | $[0,4]$ | Boole | 21.6091654882 | 21.6097764942 | $\mathbf{6.110\times10^{-4}}$ |
| 6 | $f_1$ | $[0,4]$ | Romberg | 21.6274311299 | 21.6097764942 | $1.765\times10^{-2}$ |
| 7 | $f_1$ | $[0,\pi]$ | Simpson 3/8 | 10.7583528645 | 10.7561492097 | $2.204\times10^{-3}$ |
| 8 | $f_1$ | $[0,\pi]$ | Boole | 10.7559226029 | 10.7561492097 | $\mathbf{2.266\times10^{-4}}$ |
| 9 | $f_1$ | $[0,\pi]$ | Romberg | 10.7490330989 | 10.7561492097 | $7.116\times10^{-3}$ |
| 10 | $f_2$ | $[0,2]$ | Simpson 3/8 | 1.6626596255 | 1.6620357264 | $6.239\times10^{-4}$ |
| 11 | $f_2$ | $[0,2]$ | Boole | 1.6619588007 | 1.6620357264 | $\mathbf{7.693\times10^{-5}}$ |
| 12 | $f_2$ | $[0,2]$ | Romberg | 1.6609743541 | 1.6620357264 | $1.061\times10^{-3}$ |
| 13 | $f_2$ | $[0,4]$ | Simpson 3/8 | 4.1033171781 | 4.1463131224 | $\mathbf{4.300\times10^{-2}}$ |
| 14 | $f_2$ | $[0,4]$ | Boole | 4.3179676734 | 4.1463131224 | $1.717\times10^{-1}$ |
| 15 | $f_2$ | $[0,4]$ | Romberg | 3.9247661629 | 4.1463131224 | $2.215\times10^{-1}$ |
| 16 | $f_2$ | $[0,\pi]$ | Simpson 3/8 | 3.1059749790 | 3.0793894944 | $2.659\times10^{-2}$ |
| 17 | $f_2$ | $[0,\pi]$ | Boole | 3.0802624160 | 3.0793894944 | $\mathbf{8.729\times10^{-4}}$ |
| 18 | $f_2$ | $[0,\pi]$ | Romberg | 3.2042102093 | 3.0793894944 | $1.248\times10^{-1}$ |
| 19 | $f_3$ | $[0,2]$ | Simpson 3/8 | -0.2880588885 | -0.2881320098 | $7.312\times10^{-5}$ |
| 20 | $f_3$ | $[0,2]$ | Boole | -0.2881362316 | -0.2881320098 | $\mathbf{4.222\times10^{-6}}$ |
| 21 | $f_3$ | $[0,2]$ | Romberg | -0.2881096967 | -0.2881320098 | $2.231\times10^{-5}$ |
| 22 | $f_3$ | $[0,4]$ | Simpson 3/8 | 2.5865740550 | 2.5785683966 | $8.006\times10^{-3}$ |
| 23 | $f_3$ | $[0,4]$ | Boole | 2.5778674944 | 2.5785683966 | $\mathbf{7.009\times10^{-4}}$ |
| 24 | $f_3$ | $[0,4]$ | Romberg | 2.5518710328 | 2.5785683966 | $2.670\times10^{-2}$ |
| 25 | $f_3$ | $[0,\pi]$ | Simpson 3/8 | 0.8862185981 | 0.8862190592 | $\mathbf{4.610\times10^{-7}}$ |
| 26 | $f_3$ | $[0,\pi]$ | Boole | 0.8862238182 | 0.8862190592 | $4.759\times10^{-6}$ |
| 27 | $f_3$ | $[0,\pi]$ | Romberg | 0.8851981274 | 0.8862190592 | $1.021\times10^{-3}$ |

### 5.1 Rekap Metode dengan Galat Terkecil per Kasus

| Fungsi | Batas | Metode Terbaik | Galat |
|:------:|:-----:|:---------------|------:|
| $f_1$ | $[0,2]$ | Boole | $4.657\times10^{-6}$ |
| $f_1$ | $[0,4]$ | Boole | $6.110\times10^{-4}$ |
| $f_1$ | $[0,\pi]$ | Boole | $2.266\times10^{-4}$ |
| $f_2$ | $[0,2]$ | Boole | $7.693\times10^{-5}$ |
| $f_2$ | $[0,4]$ | Simpson 3/8 | $4.300\times10^{-2}$ |
| $f_2$ | $[0,\pi]$ | Boole | $8.729\times10^{-4}$ |
| $f_3$ | $[0,2]$ | Boole | $4.222\times10^{-6}$ |
| $f_3$ | $[0,4]$ | Boole | $7.009\times10^{-4}$ |
| $f_3$ | $[0,\pi]$ | Simpson 3/8 | $4.610\times10^{-7}$ |

Jumlah kemenangan: Boole $7$ kali, Simpson 3/8 $2$ kali, Romberg $0$ kali.

### 5.2 Contoh Tabel Romberg ($f_1$ pada $[0,2]$, $K=4$)

$$
\begin{array}{c|cccc}
 & R_{k,1} & R_{k,2} & R_{k,3} & R_{k,4} \\ \hline
k=1 & 3.87445052 & & & \\
k=2 & 3.03182083 & 2.75094427 & & \\
k=3 & 2.90601038 & 2.86407356 & 2.87161551 & \\
k=4 & 2.87050002 & 2.85866324 & 2.85830255 & 2.85809124
\end{array}
$$

Nilai acuan adalah $2.85836130$. Kolom 3 ($2.85830$) sudah lebih dekat ke acuan daripada kolom 4 ($2.85809$), karena pada $K=4$ data trapesiumnya baru $8$ sub-selang. Dengan $K$ lebih besar (misalnya $6$ sampai $8$) Romberg akan jauh lebih akurat.

---

## 6. Analisis Singkat

Boole paling sering memberikan galat terkecil, yaitu pada $7$ dari $9$ kasus, sedangkan Simpson 3/8 menang pada $2$ kasus ($f_2$ di $[0,4]$ dan $f_3$ di $[0,\pi]$) dan Romberg tidak menang sama sekali. Polanya dipengaruhi panjang interval dan kemulusan fungsi: pada interval pendek $[0,2]$ semua metode sangat akurat (galat Boole $10^{-6}$ sampai $10^{-4}$), tetapi galat membesar pada $[0,4]$ karena $h$ lebih besar, dan paling parah pada $f_2$ di $[0,4]$ (galat $0.04$ sampai $0.22$) karena $\sin(x^2)$ berosilasi makin cepat (periode sekitar $0.78$ di dekat $x=4$, sedangkan $h = 1/3$) dan $x^3e^{-x}$ memiliki puncak di sekitar $x=3$, sehingga $13$ titik tidak cukup menangkap bentuk fungsinya. Romberg dengan $K=4$ hanya memakai $9$ titik ($h=0.5$ pada $[0,4]$), sehingga kalah dari dua metode lain yang memakai $13$ titik; ini bukan karena metodenya lebih lemah. Untuk sistem nyata saya memilih Romberg dengan $K$ yang ditingkatkan secara adaptif (atau kuadratur adaptif seperti `integrate()`), karena ia memberi estimasi galat dari selisih antar-level, galat turun sangat cepat untuk fungsi mulus, dan tidak perlu menebak $n$ di awal. Bila fungsi osilatif atau punya puncak tajam, kuadratur adaptif lebih aman daripada Newton–Cotes dengan $n$ tetap, dan bila harus memakai $n$ tetap, Boole adalah pilihan terbaik pada data ini.
