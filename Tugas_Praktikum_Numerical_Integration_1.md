# Latihan Modul 7 Komputasi Statistik
## Numerical Integration 1 – Metode Riemann, Trapesium, dan Simpson 1/3

**Tim Dosen Komputasi Statistik – Sains Data - ITERA**

**Nama / NIM:** Fitra / 124450102 

---

## Persiapan: Fungsi Integrasi Numerik (dari Modul)

Tiga fungsi berikut dipakai pada semua soal di bawah.

```r
# ==============================================================
# Fungsi Riemann Fleksibel
# ==============================================================
riemann <- function(f, a, b, n = 10, type = "left") {
  h <- (b - a) / n          # panjang tiap panel
  sum <- 0                  # inisialisasi penjumlahan

  for (i in 1:n) {
    # Tentukan titik x_i sesuai tipe
    if (type == "left") {
      x_i <- a + (i - 1) * h
    } else if (type == "right") {
      x_i <- a + i * h
    } else if (type == "mid") {
      x_i <- a + (i - 0.5) * h
    } else {
      stop("Tipe harus salah satu dari: 'left', 'right', atau 'mid'.")
    }

    sum <- sum + f(x_i)     # akumulasi nilai fungsi
  }

  return(sum * h)           # total luas aproksimasi
}

# ==============================================================
# Trapesium Majemuk
# ==============================================================
trapezoid <- function(f, a, b, n = 10, show_steps = FALSE) {
  # --- Validasi input ---
  if (n < 1) stop("n harus lebih besar dari 0.")
  if (!is.function(f)) stop("Argumen f harus berupa fungsi.")

  # --- Panjang panel ---
  h <- (b - a) / n

  # --- Inisialisasi hasil penjumlahan ---
  S <- f(a) + f(b)

  # --- Perulangan untuk titik tengah ---
  for (i in 1:(n - 1)) {
    x_i <- a + i * h
    S <- S + 2 * f(x_i)
    if (show_steps) cat(sprintf("i=%2d | x_i = %.4f | f(x_i) = %.4f\n", i, x_i, f(x_i)))
  }

  # --- Hasil akhir ---
  I <- (h / 2) * S
  return(I)
}

# ==============================================================
# Simpson 1/3 Majemuk
# ==============================================================
simpson13 <- function(f, a, b, n = 10, show_steps = FALSE) {
  # --- Validasi input ---
  if (!is.function(f)) stop("Argumen f harus berupa fungsi.")
  if (n %% 2 != 0) stop("Untuk Simpson 1/3, n harus GENAP.")
  if (n < 2) stop("n minimal 2 (dan harus genap).")

  # --- Panjang panel ---
  h <- (b - a) / n

  # --- Inisialisasi jumlah ---
  S <- f(a) + f(b)

  # --- Perulangan untuk titik dalam ---
  for (i in 1:(n - 1)) {
    x_i <- a + i * h
    coef <- if (i %% 2 == 0) 2 else 4
    S <- S + coef * f(x_i)
    if (show_steps) {
      cat(sprintf("i=%2d | x_i = %.4f | f(x_i) = %.6f | koef = %d\n", i, x_i, f(x_i), coef))
    }
  }

  # --- Rumus akhir Simpson 1/3 ---
  I <- (h / 3) * S
  return(I)
}
```

---

## Soal 1

> Buatlah dua fungsi berbeda yang akan diintegralkan secara numerik dengan metode Riemann, Trapesium, dan Simpson 1/3 berikut ini:
>
> (a) Fungsi polinomial sederhana: f1(x) = x² + 3x + 2 dengan batas integrasi a = 0, b = 4, dan n = 8.
>
> (b) Fungsi nonlinier (eksponensial–trigonometri): f2(x) = e^(−x) sin(x) dengan batas integrasi a = 0, b = π, dan n = 10.
>
> Gunakan ketiga variasi metode riemann() untuk Riemann kiri, kanan, dan tengah pada kedua fungsi tersebut.

```r
# ==============================================================
# SOAL 1: Definisi fungsi dan Riemann kiri, kanan, tengah
# ==============================================================

# --- (a) Fungsi polinomial ---
f1 <- function(x) x^2 + 3*x + 2
a1 <- 0
b1 <- 4
n1 <- 8

riemann_left_1  <- riemann(f1, a1, b1, n1, type = "left")
riemann_right_1 <- riemann(f1, a1, b1, n1, type = "right")
riemann_mid_1   <- riemann(f1, a1, b1, n1, type = "mid")

cat("=== Fungsi 1: x^2 + 3x + 2, [0, 4], n = 8 ===\n")
cat(sprintf("Riemann Kiri   : %.6f\n", riemann_left_1))
cat(sprintf("Riemann Kanan  : %.6f\n", riemann_right_1))
cat(sprintf("Riemann Tengah : %.6f\n\n", riemann_mid_1))

# --- (b) Fungsi eksponensial-trigonometri ---
f2 <- function(x) exp(-x) * sin(x)
a2 <- 0
b2 <- pi
n2 <- 10

riemann_left_2  <- riemann(f2, a2, b2, n2, type = "left")
riemann_right_2 <- riemann(f2, a2, b2, n2, type = "right")
riemann_mid_2   <- riemann(f2, a2, b2, n2, type = "mid")

cat("=== Fungsi 2: exp(-x) sin(x), [0, pi], n = 10 ===\n")
cat(sprintf("Riemann Kiri   : %.6f\n", riemann_left_2))
cat(sprintf("Riemann Kanan  : %.6f\n", riemann_right_2))
cat(sprintf("Riemann Tengah : %.6f\n", riemann_mid_2))
```

**Hasil**

```
=== Fungsi 1: x^2 + 3x + 2, [0, 4], n = 8 ===
Riemann Kiri   : 46.500000
Riemann Kanan  : 60.500000
Riemann Tengah : 53.250000

=== Fungsi 2: exp(-x) sin(x), [0, pi], n = 10 ===
Riemann Kiri   : 0.513055
Riemann Kanan  : 0.513055
Riemann Tengah : 0.525872
```

**Interpretasi**

- **Fungsi 1:** f1 naik monoton pada [0, 4], sehingga Riemann kiri (tinggi panel diambil di ujung kiri, yang paling rendah) *underestimate* (46,5) dan Riemann kanan *overestimate* (60,5). Riemann tengah (53,25) berada di antara keduanya dan sudah sangat dekat dengan nilai sebenarnya karena galat di sisi kiri dan kanan titik tengah saling meniadakan.
- **Fungsi 2:** Riemann kiri dan kanan bernilai sama (0,513055) karena f2(0) = f2(π) = 0, dan nilai fungsi di titik-titik dalam selang hanya bergeser satu panel sehingga jumlahnya identik. Riemann tengah (0,525872) sedikit di atas nilai sebenarnya, tetapi galatnya sekitar setengah dari kiri/kanan.

---

## Soal 2

> Hitung juga integral kedua fungsi tersebut menggunakan trapezoid() dan simpson13() yang telah dibuat sebelumnya, dengan jumlah panel n sesuai masing-masing fungsi.

```r
# ==============================================================
# SOAL 2: Trapesium dan Simpson 1/3
# ==============================================================

# --- Fungsi 1 (n = 8) ---
trapezoid_1 <- trapezoid(f1, a1, b1, n1)
simpson_1   <- simpson13(f1, a1, b1, n1)

cat("=== Fungsi 1 (n = 8) ===\n")
cat(sprintf("Trapesium   : %.6f\n", trapezoid_1))
cat(sprintf("Simpson 1/3 : %.6f\n\n", simpson_1))

# --- Fungsi 2 (n = 10) ---
trapezoid_2 <- trapezoid(f2, a2, b2, n2)
simpson_2   <- simpson13(f2, a2, b2, n2)

cat("=== Fungsi 2 (n = 10) ===\n")
cat(sprintf("Trapesium   : %.6f\n", trapezoid_2))
cat(sprintf("Simpson 1/3 : %.6f\n", simpson_2))
```

**Hasil**

```
=== Fungsi 1 (n = 8) ===
Trapesium   : 53.500000
Simpson 1/3 : 53.333333

=== Fungsi 2 (n = 10) ===
Trapesium   : 0.513055
Simpson 1/3 : 0.521491
```

**Interpretasi**

- **Fungsi 1:** Trapesium menghasilkan 53,5 (sedikit lebih besar karena f1 cembung, sehingga garis lurus di atas kurva menambah luas). Simpson 1/3 menghasilkan 53,333333, tepat sama dengan nilai analitik karena parabola kuadratik yang dipakai Simpson mampu merepresentasikan polinomial berderajat 2 secara persis.
- **Fungsi 2:** Trapesium (0,513055) sama dengan Riemann kiri/kanan karena f(a) = f(b) = 0. Simpson 1/3 (0,521491) jauh lebih dekat ke nilai sebenarnya karena parabola lebih mengikuti lengkungan fungsi daripada garis lurus.
- Syarat n genap terpenuhi pada kedua kasus (n = 8 dan n = 10), sehingga `simpson13()` dapat dipakai.

---

## Soal 3

> Hitung nilai eksak kedua integral tersebut secara analitik (gunakan hasil turunan rumus integral atau bantuan fungsi integrate() di R) dan bandingkan dengan hasil numerik dari setiap metode.

**Penurunan analitik**

Fungsi 1:

$$
\int_0^4 (x^2 + 3x + 2)\,dx = \left[\frac{x^3}{3} + \frac{3x^2}{2} + 2x\right]_0^4 = \frac{64}{3} + 24 + 8 = \frac{160}{3} \approx 53{,}333333
$$

Fungsi 2 (integral parsial dua kali):

$$
\int e^{-x}\sin x\,dx = -\frac{e^{-x}(\sin x + \cos x)}{2} + C
\;\Rightarrow\;
\int_0^{\pi} e^{-x}\sin x\,dx = \frac{1 + e^{-\pi}}{2} \approx 0{,}521607
$$

```r
# ==============================================================
# SOAL 3: Nilai eksak dan perbandingan galat
# ==============================================================

# --- Fungsi 1: F(x) = x^3/3 + 3x^2/2 + 2x ---
F1 <- function(x) x^3 / 3 + 3 * x^2 / 2 + 2 * x
exact_analitik_1  <- F1(b1) - F1(a1)
exact_integrate_1 <- integrate(f1, lower = a1, upper = b1)$value

# --- Fungsi 2: F(x) = -exp(-x) (sin x + cos x) / 2 ---
F2 <- function(x) -exp(-x) * (sin(x) + cos(x)) / 2
exact_analitik_2  <- F2(b2) - F2(a2)
exact_integrate_2 <- integrate(f2, lower = a2, upper = b2)$value

cat(sprintf("Fungsi 1 | analitik: %.6f | integrate(): %.6f\n", exact_analitik_1, exact_integrate_1))
cat(sprintf("Fungsi 2 | analitik: %.6f | integrate(): %.6f\n\n", exact_analitik_2, exact_integrate_2))

# --- Galat absolut tiap metode ---
cat("Galat absolut Fungsi 1:\n")
cat(sprintf("  Riemann Kiri   : %.6f\n", abs(riemann_left_1  - exact_analitik_1)))
cat(sprintf("  Riemann Kanan  : %.6f\n", abs(riemann_right_1 - exact_analitik_1)))
cat(sprintf("  Riemann Tengah : %.6f\n", abs(riemann_mid_1   - exact_analitik_1)))
cat(sprintf("  Trapesium      : %.6f\n", abs(trapezoid_1     - exact_analitik_1)))
cat(sprintf("  Simpson 1/3    : %.6f\n\n", abs(simpson_1     - exact_analitik_1)))

cat("Galat absolut Fungsi 2:\n")
cat(sprintf("  Riemann Kiri   : %.6f\n", abs(riemann_left_2  - exact_analitik_2)))
cat(sprintf("  Riemann Kanan  : %.6f\n", abs(riemann_right_2 - exact_analitik_2)))
cat(sprintf("  Riemann Tengah : %.6f\n", abs(riemann_mid_2   - exact_analitik_2)))
cat(sprintf("  Trapesium      : %.6f\n", abs(trapezoid_2     - exact_analitik_2)))
cat(sprintf("  Simpson 1/3    : %.6f\n", abs(simpson_2       - exact_analitik_2)))
```

**Hasil**

```
Fungsi 1 | analitik: 53.333333 | integrate(): 53.333333
Fungsi 2 | analitik: 0.521607 | integrate(): 0.521607

Galat absolut Fungsi 1:
  Riemann Kiri   : 6.833333
  Riemann Kanan  : 7.166667
  Riemann Tengah : 0.083333
  Trapesium      : 0.166667
  Simpson 1/3    : 0.000000

Galat absolut Fungsi 2:
  Riemann Kiri   : 0.008552
  Riemann Kanan  : 0.008552
  Riemann Tengah : 0.004265
  Trapesium      : 0.008552
  Simpson 1/3    : 0.000116
```

**Interpretasi**

- Hasil analitik dan `integrate()` identik di kedua fungsi, jadi nilai eksak 53,333333 (f1) dan 0,521607 (f2) dapat dijadikan acuan yang valid.
- Pada f1, galat Riemann kiri dan kanan sekitar 7 (± 13% dari nilai eksak), jauh lebih besar dibanding Riemann tengah (0,083) dan Trapesium (0,167). Simpson 1/3 bergalat nol.
- Pada f2, galat Simpson (1,16 × 10⁻⁴) sekitar 37 kali lebih kecil daripada Riemann tengah (4,27 × 10⁻³), dan sekitar 74 kali lebih kecil daripada Trapesium (8,55 × 10⁻³).

---

## Soal 4

> Buat tabel perbandingan nilai hasil integral untuk masing-masing fungsi:
> - Riemann kiri, kanan, tengah
> - Trapesium
> - Simpson 1/3
> - Nilai eksak

```r
# ==============================================================
# SOAL 4: Tabel perbandingan
# ==============================================================
metode <- c("Riemann Kiri", "Riemann Kanan", "Riemann Tengah",
            "Trapesium", "Simpson 1/3", "Nilai Eksak")

tabel1 <- data.frame(
  Metode = metode,
  Hasil  = c(riemann_left_1, riemann_right_1, riemann_mid_1,
             trapezoid_1, simpson_1, exact_analitik_1)
)

tabel2 <- data.frame(
  Metode = metode,
  Hasil  = c(riemann_left_2, riemann_right_2, riemann_mid_2,
             trapezoid_2, simpson_2, exact_analitik_2)
)

cat("Fungsi 1: f1(x) = x^2 + 3x + 2, a = 0, b = 4, n = 8\n")
print(tabel1, digits = 7)
cat("\nFungsi 2: f2(x) = e^-x sin(x), a = 0, b = pi, n = 10\n")
print(tabel2, digits = 7)
```

**Hasil**

**Fungsi 1: f1(x) = x² + 3x + 2, a = 0, b = 4, n = 8**

| Metode | Hasil Integral |
|--------|---------------:|
| Riemann Kiri | 46,500000 |
| Riemann Kanan | 60,500000 |
| Riemann Tengah | 53,250000 |
| Trapesium | 53,500000 |
| Simpson 1/3 | 53,333333 |
| **Nilai Eksak** | **53,333333** |

**Fungsi 2: f2(x) = e^(−x) sin(x), a = 0, b = π, n = 10**

| Metode | Hasil Integral |
|--------|---------------:|
| Riemann Kiri | 0,513055 |
| Riemann Kanan | 0,513055 |
| Riemann Tengah | 0,525872 |
| Trapesium | 0,513055 |
| Simpson 1/3 | 0,521491 |
| **Nilai Eksak** | **0,521607** |

**Interpretasi**

- Pada kedua tabel, hanya Simpson 1/3 yang konsisten paling dekat dengan nilai eksak; Riemann kiri dan kanan paling jauh pada f1.
- Pada f1, Riemann kiri dan kanan berada di sisi berlawanan dari nilai eksak (46,5 < 53,33 < 60,5), sedangkan Trapesium dan Riemann tengah berada sangat dekat di kedua sisi.
- Pada f2, tiga metode (Riemann kiri, kanan, Trapesium) memberi nilai sama karena f(0) = f(π) = 0. Riemann tengah overestimate, sementara metode lain underestimate.

---

## Soal 5

> Tuliskan analisis singkat:
> - Metode mana yang memberikan hasil paling akurat untuk masing-masing fungsi?
> - Bagaimana pengaruh jumlah panel (n) terhadap akurasi hasil?

```r
# ==============================================================
# SOAL 5: Pengaruh jumlah panel (n) terhadap galat (Fungsi 2)
# ==============================================================
cat("Galat absolut Fungsi 2 untuk berbagai n:\n")
for (n in c(2, 4, 8, 10, 20, 40, 80)) {
  err_mid  <- abs(riemann(f2, a2, b2, n, "mid") - exact_analitik_2)
  err_trap <- abs(trapezoid(f2, a2, b2, n)      - exact_analitik_2)
  err_simp <- abs(simpson13(f2, a2, b2, n)      - exact_analitik_2)
  cat(sprintf("n = %2d | Riemann tengah: %.2e | Trapesium: %.2e | Simpson: %.2e\n",
              n, err_mid, err_trap, err_simp))
}
```

**Hasil**

| n | Riemann Tengah | Trapesium | Simpson 1/3 |
|--:|---------------:|----------:|------------:|
| 2 | 9,01e-02 | 1,95e-01 | 8,62e-02 |
| 4 | 2,58e-02 | 5,25e-02 | 4,97e-03 |
| 8 | 6,64e-03 | 1,33e-02 | 2,85e-04 |
| 10 | 4,27e-03 | 8,55e-03 | 1,16e-04 |
| 20 | 1,07e-03 | 2,14e-03 | 7,10e-06 |
| 40 | 2,68e-04 | 5,36e-04 | 4,42e-07 |
| 80 | 6,70e-05 | 1,34e-04 | 2,76e-08 |

**Analisis**

**a. Metode paling akurat**

- **Fungsi 1 (polinomial):** Simpson 1/3, hasilnya tepat sama dengan nilai eksak karena Simpson 1/3 eksak untuk polinomial berderajat ≤ 3 dan f1 berderajat 2. Urutan berikutnya: Riemann tengah (galat 0,083), Trapesium (0,167), lalu Riemann kiri dan kanan (galat ≈ 7).
- **Fungsi 2 (eksponensial–trigonometri):** Simpson 1/3 dengan galat ≈ 1,2 × 10⁻⁴, diikuti Riemann tengah (4,3 × 10⁻³), lalu Trapesium dan Riemann kiri/kanan (8,6 × 10⁻³).
- Alasan umum: Simpson memakai parabola sebagai pendekatan lokal, Trapesium memakai garis lurus, dan Riemann memakai garis datar. Semakin tinggi orde interpolasinya, semakin kecil galatnya untuk fungsi yang mulus.

**b. Pengaruh jumlah panel (n)**

- Semakin besar n, panel semakin sempit (h mengecil) dan hasil semakin dekat dengan nilai eksak untuk semua metode.
- Laju penurunan galat berbeda tiap metode. Riemann kiri/kanan berorde O(h), jadi n dua kali lipat menurunkan galat menjadi sekitar setengahnya. Trapesium dan Riemann tengah berorde O(h²), jadi galat turun menjadi sekitar seperempat. Simpson 1/3 berorde O(h⁴), jadi galat turun menjadi sekitar 1/16 (pada tabel, dari n = 40 ke n = 80 galat turun dari 4,42e-07 menjadi 2,76e-08).
- Konsekuensinya, menambah n menambah jumlah evaluasi fungsi. Simpson 1/3 mencapai akurasi tinggi dengan n kecil, tetapi n harus genap.

**Kesimpulan**

Untuk fungsi yang mulus, urutan akurasi dari tertinggi ke terendah adalah **Simpson 1/3 > Riemann tengah ≈ Trapesium > Riemann kiri/kanan**, dan memperbesar n selalu memperkecil galat.
