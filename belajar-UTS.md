# Panduan Belajar Kuis Komputasi Statistik

Isi file ini: **tips pilihan ganda → materi inti per bab → soal tebak output (pilihan ganda) + pembahasan**.

Soal diambil dan diringkas dari file latihan (130 soal) menjadi **68 soal** yang paling mungkin muncul. Semua kode yang ditanyakan berupa **tebak output**.

> **Cara pakai:** baca materi per bab, lalu kerjakan soal dengan menutup bagian "Jawaban" dulu. Coba tulis tebakanmu di kertas sebelum melihat pembahasan.
>
> **Catatan jujur:** R tidak tersedia di lingkungan saya. Output deterministik saya telusuri manual, dan nilai peluang saya cek dengan Python (scipy). Kalau hasil di R-mu berbeda, percaya R-nya. Ada dua koreksi untuk file latihan aslimu, lihat bagian **Koreksi** di akhir.

---

# Bagian 0 — Tips Mengerjakan Soal Tebak Output Pilihan Ganda

1. **Cek dulu: apakah kode ini error?** Baca nama fungsi dan objek huruf demi huruf (`betas` vs `bbetas`, `rnom`, `matriks`). Panjang kolom data frame juga harus sama. Dosen suka menaruh jawaban "Error" sebagai opsi yang benar.
2. **Telusuri dengan tabel kecil (trace table)** untuk `for`/`while`/`repeat`. Tulis nilai variabel di tiap iterasi, jangan ditebak di kepala.
3. **Perhatikan bentuk output, bukan hanya isinya.** Opsi yang isinya sama sering berbeda di tanda kutip atau `[1]`.
   - `print()`/ketik objek → ada `[1]`; `cat()` → **tanpa** `[1]`.
   - Karakter → bertanda kutip `"A"`; factor → tanpa kutip + baris `Levels:`.
   - Vektor bernama → nama di atas, nilai di bawah.
4. **Soal acak (`runif`, `rnorm`, `sample`)**: nilai persis tidak bisa ditebak. Yang ditanyakan adalah **sifat**: panjang (pasti), rentang, perkiraan rata-rata. Pilih opsi dengan kata "kira-kira/mendekati" dan jangan terjebak opsi yang menyebut angka persis.
5. **Eliminasi opsi** dengan memeriksa hal yang pasti dulu: panjang vektor, dimensi, tipe data, banyak baris. Biasanya dua opsi gugur hanya dari ini.
6. **Dua opsi yang nyaris sama**: bedanya hampir selalu satu detail (`<` vs `<=`, `14` vs `15`, `byrow`, `right = TRUE`). Cari detail itu.
7. **Urutan operator**: `^` lebih dulu dari `*`, `%%` dan `%/%` sebelum `+`. Ingat `-7 %% 3 = 2` (hasil mengikuti tanda pembagi).
8. **Kalau waktu mepet**, lewati soal kode panjang (rekursi, merge) dan kerjakan soal konsep/singkat dulu.

---

# Bagian 1 — Objek & Struktur Data R

## Materi inti

- **Nama objek**: huruf, angka, `.`, `_`; harus diawali huruf atau titik (titik tidak boleh langsung diikuti angka); **case sensitive**; tanpa spasi dan karakter khusus.
- **Assignment**: `<-`, `=`, `assign("x", 5)`. Kelola objek: `objects()` / `ls()`, `rm(a)`, `rm(list = objects())`.
- **Hierarki tipe (coercion)**: logical → integer → numeric → complex → **character**. Campuran akan "naik" ke tipe tertinggi.
- `class(5)` = `"numeric"`, `typeof(5)` = `"double"`, `class(5L)` = `"integer"`.
- **Vektor**: `c()`, `:`, `seq(from, to, by, length.out, along.with)`, `rep(x, times, each)`, `paste(..., sep = " ")`, `paste0()` (tanpa pemisah). Indeks mulai dari **1**, indeks negatif membuang elemen. Operasi vektor memakai *recycling*.
- **Matriks**: `matrix(data, nrow, ncol, byrow = FALSE)` terisi **per kolom**. `cbind` (gabung kolom), `rbind` (gabung baris), `m[i, j]`, `m[i, ]`, `m[, j]`.
- **Array**: `array(data, dim = c(baris, kolom, lapisan))`, diakses `a[i, j, k]`.
- **Factor**: menyimpan kategori sebagai level. Level diurutkan **alfabet**, kode integer mengikuti urutan itu.
- **Data frame**: semua kolom **harus sama panjang**, tipe antar kolom boleh beda.
- **List**: `[ ]` mengembalikan **list**, `[[ ]]` / `$` mengeluarkan **isi** elemen.

## Soal

### Soal 1
Nama objek manakah yang **valid** di R?

- A. `2var`
- B. `.x`
- C. `my var`
- D. `_abc`

**Jawaban: B**
`2var` diawali angka, `my var` ada spasi, `_abc` diawali garis bawah. `.x` valid karena titik boleh di depan (asal tidak langsung diikuti angka). Ingat juga: R *case sensitive*, jadi `Komputasi <- 5; komputasi <- 10; Komputasi + komputasi` menghasilkan `15`, bukan error.

---

### Soal 2
```r
class(5L)
typeof(5)
is.integer(5)
```
Output yang benar secara berurutan:

- A. `"integer"`, `"double"`, `FALSE`
- B. `"numeric"`, `"double"`, `TRUE`
- C. `"integer"`, `"numeric"`, `FALSE`
- D. `"numeric"`, `"integer"`, `TRUE`

**Jawaban: A**
Akhiran `L` membuat bilangan bertipe integer. Angka `5` tanpa `L` disimpan sebagai **double** (kelasnya `numeric`), sehingga `is.integer(5)` adalah `FALSE`. Sebaliknya `is.numeric(5L)` adalah `TRUE`.

---

### Soal 3
```r
a <- c(1, "a", TRUE)
b <- c(TRUE, FALSE, 3L)
a
b
```
- A. `[1] 1 "a" TRUE` dan `[1] TRUE FALSE 3`
- B. `[1] "1" "a" "TRUE"` dan `[1] 1 0 3`
- C. `[1] "1" "a" "TRUE"` dan `[1] "TRUE" "FALSE" "3"`
- D. Error karena tipe data campur

**Jawaban: B**
Vektor hanya boleh satu tipe. Ada karakter di `a`, sehingga semuanya menjadi karakter. Di `b` tipe tertinggi adalah integer, sehingga `TRUE` menjadi `1` dan `FALSE` menjadi `0`.

---

### Soal 4
```r
paste0("A", 1:4, sep = "-")
```
- A. `"A-1" "A-2" "A-3" "A-4"`
- B. `"A1" "A2" "A3" "A4"`
- C. `"A1-" "A2-" "A3-" "A4-"`
- D. `"A 1" "A 2" "A 3" "A 4"`

**Jawaban: C**
`paste0` tidak punya argumen pemisah, sehingga `sep = "-"` diperlakukan sebagai **objek tambahan** yang ikut digabung di belakang. Jebakan klasik. (Dengan `paste("A", 1:4, sep = "")` hasilnya `"A1" ...`.)

---

### Soal 5
```r
rep(1:3, each = 2)
rep(c("a", "b"), times = c(3, 1))
```
- A. `1 2 3 1 2 3` dan `"a" "b" "a" "a"`
- B. `1 1 2 2 3 3` dan `"a" "b" "a" "a"`
- C. `1 2 3 1 2 3` dan `"a" "a" "a" "b"`
- D. `1 1 2 2 3 3` dan `"a" "a" "a" "b"`

**Jawaban: D**
`each = 2` mengulang **tiap elemen** dua kali berturut-turut. `times = c(3, 1)` mengulang elemen pertama 3 kali dan elemen kedua 1 kali. (`rep(1:3, 2)` tanpa `each` barulah `1 2 3 1 2 3`.)

---

### Soal 6
```r
x <- c(5, 3, 8, 1)
order(x)
c(1, 2, 3, 4) + c(10, 20)
```
- A. `[1] 4 2 1 3` dan `[1] 11 22 13 24`
- B. `[1] 1 3 5 8` dan `[1] 11 22 13 24`
- C. `[1] 4 2 1 3` dan `[1] 11 22`
- D. `[1] 4 2 1 3` dan error

**Jawaban: A**
`order()` mengembalikan **posisi** (indeks) elemen jika diurutkan naik: terkecil adalah 1 di posisi 4, lalu 3 di posisi 2, 5 di posisi 1, 8 di posisi 3. Yang mengembalikan nilai terurut adalah `sort()`, yaitu opsi B. Penjumlahan memakai *recycling*: `(1,2,3,4) + (10,20,10,20)`. Tidak ada warning karena 4 kelipatan 2.

---

### Soal 7
```r
m <- matrix(1:6, 2, 3)
m[1, ]
m[, 2]
```
- A. `1 2` dan `3 4`
- B. `1 2 3` dan `3 4`
- C. `1 3 5` dan `3 4`
- D. `1 3 5` dan `2 4`

**Jawaban: C**
Matriks diisi per kolom (`byrow = FALSE`):
```
     [,1] [,2] [,3]
[1,]    1    3    5
[2,]    2    4    6
```
Baris 1 = `1 3 5`, kolom 2 = `3 4`.

---

### Soal 8
```r
m1 <- matrix(2:5, 2)
m2 <- matrix(5:8, 2)
dim(rbind(m1, m2))
dim(cbind(m1, m2))
m1[2, 1]
```
- A. `2 4`, `4 2`, `3`
- B. `4 2`, `2 4`, `3`
- C. `4 2`, `2 4`, `4`
- D. `4 2`, `2 4`, `2`

**Jawaban: B**
`m1` = `[2 4; 3 5]`, `m2` = `[5 7; 6 8]`. `rbind` menumpuk **baris** → 4 baris × 2 kolom. `cbind` menyambung **kolom** → 2 baris × 4 kolom. `m1[2,1]` = baris 2, kolom 1 = `3`.

---

### Soal 9
```r
arr <- array(LETTERS, dim = c(2, 4, 3))
arr[2, 3, 1]
```
- A. `"E"`
- B. `"G"`
- C. `"H"`
- D. `"F"`

**Jawaban: D**
Lapisan 1 berisi 8 huruf pertama, diisi per kolom:
```
     [,1] [,2] [,3] [,4]
[1,]  A    C    E    G
[2,]  B    D    F    H
```
Baris 2, kolom 3 = `"F"`. Pada `arr[2, 3, 1]`, angka ketiga adalah nomor **lapisan**.

---

### Soal 10
```r
f <- factor(c("A", "A", "O", "B", "AB", "A"))
as.integer(f)
nlevels(f)
```
- A. `1 1 4 3 2 1` dan `4`
- B. `1 1 2 3 4 1` dan `4`
- C. `1 1 4 3 2 1` dan `6`
- D. `1 1 3 2 4 1` dan `4`

**Jawaban: A**
Level diurutkan alfabet: `A`(1), `AB`(2), `B`(3), `O`(4). Maka data `A A O B AB A` menjadi kode `1 1 4 3 2 1`. Jumlah level = 4 (kategori unik), bukan 6 (panjang data). Urutan level **bukan** urutan kemunculan.

---

### Soal 11
```r
nama    <- c("Bian", "Cian", "Dian")
tb      <- c(160.0, 164.5, 156.8)
saudara <- c(2, 2, 1)
sekolah <- factor(c("SD", "SMP", "SMA", "SMA"))
df <- data.frame(nama, tb, saudara, sekolah)
```
- A. Data frame 3 baris, `sekolah` berisi `NA` pada baris tambahan
- B. Data frame 4 baris, kolom lain didaur ulang
- C. Error: `arguments imply differing number of rows: 3, 4`
- D. Data frame 3 baris, elemen keempat `sekolah` diabaikan

**Jawaban: C**
Kolom data frame **harus sama panjang**. Tiga kolom panjang 3, satu kolom panjang 4, jadi error. Ini tipe soal "jebakan" yang sering muncul.

---

### Soal 12
```r
list1 <- list("man", c(78, 66, 98), TRUE, 156.1)
class(list1[2])
class(list1[[2]])
list1[[2]][3]
```
- A. `"numeric"`, `"numeric"`, `98`
- B. `"list"`, `"numeric"`, `98`
- C. `"list"`, `"list"`, `66`
- D. `"numeric"`, `"list"`, `98`

**Jawaban: B**
`[ ]` pada list mengembalikan **list** (sub-list), `[[ ]]` mengeluarkan **isi** elemen (vektor numerik). `list1[[2]][3]` = elemen ke-3 dari `c(78, 66, 98)` = `98`.

---

# Bagian 2 — Algoritma & Pemrograman Dasar

## Materi inti

- **5 ciri algoritma**: *Finiteness* (berhingga), *Definiteness* (pasti, tidak ambigu), *Input* (nol atau lebih), *Output* (nol atau lebih), *Effectiveness* (efektif/sangkil).
- **3 struktur**: runtunan (berurutan), percabangan (`if`, `else if`, `else`), perulangan.
- **Perulangan**:
  - `for`: jumlah iterasi **diketahui**.
  - `while`: jumlah iterasi tidak diketahui, cek kondisi **di awal** (bisa tidak pernah jalan).
  - `repeat`: jalan **minimal sekali**, wajib ada `break`.
  - `break` menghentikan loop, `next` melompat ke iterasi berikutnya.
- Soal tipe **trace**: algoritma mencari maksimum/minimum, metode Newton (`x - f(x)/f'(x)`), pengurutan, deret, fibonacci, pinjaman.

## Soal

### Soal 13
Manakah yang **BUKAN** ciri algoritma?

- A. Finiteness
- B. Definiteness
- C. Recursiveness
- D. Effectiveness

**Jawaban: C**
Lima ciri: finiteness, definiteness, input, output, effectiveness. Rekursi adalah teknik pemrograman, bukan ciri algoritma.

---

### Soal 14
```r
i <- 1
while (i < 6) {
  print(i)
  i <- i + 1
  if (i == 4) {
    break
  }
}
```
Angka apa saja yang dicetak?

- A. `1`, `2`, `3`
- B. `1`, `2`, `3`, `4`, `5`
- C. `1`, `2`, `3`, `4`
- D. `1`, `2`

**Jawaban: A**
Trace: `print(1)`, `i`=2; `print(2)`, `i`=3; `print(3)`, `i`=4 → `break`. Angka 4 **tidak pernah dicetak**, karena `print` ada sebelum penambahan `i` dan `break` terjadi sesudahnya.

---

### Soal 15
```r
i <- 0
repeat {
  i <- i + 1
  if (i %% 2 == 0) next
  if (i > 7) break
  cat(i, " ")
}
```
- A. `[1] 1 3 5 7`
- B. `1  3  5  7`
- C. `1 3 5 7 9`
- D. `2 4 6 8`

**Jawaban: B**
`next` melewati bilangan genap. Saat `i = 9` (ganjil) kondisi `i > 7` terpenuhi, `break` terjadi **sebelum** `cat`, jadi 9 tidak tercetak. `cat` tidak mencetak `[1]`, dan tiap angka diikuti spasi (separator dan `" "`).

---

### Soal 16
```r
x = c(15, 18, 10, 8, 20)
n = length(x)
for (i in 2:n) {
  if (x[i] > x[1]) {
    a = x[1]
    x[1] = x[i]
    x[i] = a
  }
}
a = x[1]
a
```
Output `a` dan isi akhir `x`:

- A. `20`, `x` = `20 18 15 10 8`
- B. `18`, `x` = `18 15 10 8 20`
- C. `20`, `x` tidak berubah
- D. `20`, `x` = `20 15 10 8 18`

**Jawaban: D**

| i | x[1] sebelum | x[i] | tukar? | x sesudah |
|---|---|---|---|---|
| 2 | 15 | 18 | ya | 18 15 10 8 20 |
| 3 | 18 | 10 | tidak | sama |
| 4 | 18 | 8 | tidak | sama |
| 5 | 18 | 20 | ya | **20 15 10 8 18** |

Algoritma ini mencari **nilai maksimum**. Perhatikan bahwa vektor ikut berubah (18 pindah ke posisi 5), bukan terurut. Jika kondisi diganti `<`, algoritma mencari **minimum** (hasil 8).

---

### Soal 17 (Newton)
```r
X = 1
E = 2
while (E > 10**(-5)) {
  C = X
  X = X - (5*X**2 - 1.25)/(10*X)
  E = abs(X - C)
}
X
```
Nilai `X` setelah iterasi pertama dan output akhir:

- A. `0.625` dan `0.5`
- B. `0.5` dan `0.625`
- C. `0.625` dan `0.25`
- D. `1.25` dan `0.5`

**Jawaban: A**
Ini metode Newton-Raphson untuk `f(x) = 5x² − 1.25` dengan `f'(x) = 10x`. Akar positifnya `x = 0.5`.
Iterasi 1: `1 − 3.75/10 = 0.625`. Iterasi 2: `0.5125`. Iterasi 3: `0.5001524`. Iterasi 4: `0.50000002`. Iterasi 5: selisih ≈ 2×10⁻⁸ < 10⁻⁵, jadi loop berhenti (5 kali). Output `[1] 0.5`.

---

### Soal 18 (pengurutan)
```r
x = c(15, 18, 10, 8, 20)
i = 1
for (j in 2:5) {
  if (x[i] > x[j]) {
    t = x[i]; x[i] = x[j]; x[j] = t
  }
}
x
```
Isi `x` setelah loop selesai:

- A. `8 18 10 15 20`
- B. `10 18 15 8 20`
- C. `8 18 15 10 20`
- D. `8 10 15 18 20`

**Jawaban: C**
Bandingkan `x[1]` dengan tiap elemen lain dan tukar bila `x[1]` lebih besar.
- j=2: 15 > 18? tidak.
- j=3: 15 > 10, tukar → `10 18 15 8 20`.
- j=4: 10 > 8, tukar → `8 18 15 10 20`.
- j=5: 8 > 20? tidak.

Setelah `i = 1`, posisi 1 sudah berisi nilai terkecil. Data baru terurut penuh (D) setelah semua `i` selesai.

---

### Soal 19 (pinjaman)
```r
saldo = 10000000
bulan = 0
while (saldo > 0) {
  saldo = saldo * 1.01 - 1000000
  bulan = bulan + 1
}
bulan
saldo
```
- A. `10` dan `584008.7`
- B. `11` dan `-410151.2`
- C. `11` dan `0`
- D. `12` dan `-1410151`

**Jawaban: B**
Saldo setelah bulan ke-1, 2, 3: 9.100.000; 8.191.000; 7.272.910. Pada bulan ke-10 saldo masih **584.008,7 (> 0)**, jadi loop lanjut. Bulan ke-11: `584008.7 × 1.01 − 1000000 = −410151.2` (≤ 0), loop berhenti. Saldo akhir **negatif**, bukan 0, karena kondisi hanya diperiksa di awal tiap iterasi.

---

### Soal 20 (deret)
```r
e = 10; z0 = 0; i = 1
while (e > 10**(-5)) {
  z1 = z0 + (2*i + 1)/(i**2 + 2)
  e = abs(z1 - z0)
  z0 = z1
  i = i + 1
}
```
Mengapa loop ini akhirnya berhenti, padahal deretnya divergen?

- A. Karena deret konvergen ke suatu nilai
- B. Karena kondisi berhenti memeriksa besar **suku terakhir** (`e`), dan suku ≈ 2/i mengecil hingga < 10⁻⁵, walaupun jumlahnya terus naik
- C. Karena R membatasi loop `while` maksimal 1000 iterasi
- D. Karena `z0` melebihi batas bilangan komputer

**Jawaban: B**
Suku `(2i+1)/(i²+2) ≈ 2/i` seperti deret harmonik, yang **divergen**. Tetapi `e` hanya membandingkan `z1` dengan `z0`, yaitu besar satu suku. Suku itu mengecil di bawah `1e-5` ketika `i` sekitar 200 ribu. Karena itu loop berjalan sangat lama (±200 ribu iterasi), dan `z0` berhenti di kira-kira 24,49. Hentinya loop **tidak membuktikan** deret konvergen.

---

# Bagian 3 — Munging / Wrangling Data

## Materi inti

- **Variabel baru**: `df$baru <- df$a + df$b`.
- **Subsetting**: `df[kondisi, ]`, `df[kondisi, "kolom"]`, `subset(df, kondisi)`. Gabung kondisi dengan `&`, `|`, `!`. Baris `NA` pada kondisi logis butuh `!is.na()`.
- **Sorting**: `sort(x)` mengembalikan **nilai terurut**; `order(x)` mengembalikan **indeks**. `decreasing = TRUE` untuk menurun. `NA` ditaruh paling akhir. Data kembar mempertahankan urutan asli.
- **Recoding**: `ifelse(kondisi, ya, tidak)`, `cut(x, breaks, labels)`, `recode()` (paket `car`), `factor()`.
- **`cut()` default `right = TRUE`**, yaitu interval **(a, b]** (tutup kanan).
- **Merging**: `cbind`/`rbind` (menempel), `merge(x, y, by.x, by.y, all, all.x, all.y)`:
  - `all = FALSE` → inner join (hanya yang cocok)
  - `all.x = TRUE` → left join (semua baris x)
  - `all.y = TRUE` → right join (semua baris y)
  - `all = TRUE` → full join
- **Reshaping**: `reshape(..., direction = "wide")` (long → wide, memakai `idvar` dan `timevar`) dan `direction = "long"` (wide → long, memakai `varying`).

**Data yang dipakai soal 21–23:**
```
   JK  T1  T2  T3  T4
1   L 100 102  90 134
2   P 105 105 150 135
3   L 100  98 156 137
4   L 110  99 156 138
5   P 150 151 180 140
6   L 130 160 110 150
7   P  99  NA 165 155
8   P 164 100 154 165
9   P 134 154 123 170
10  P 125 155  90 130
```

## Soal

### Soal 21
```r
Tinggi3 = (GabungT$JK == 'L' & GabungT$T1 > 100 & GabungT$T4 < 150) |
          (GabungT$JK == 'P' & !is.na(GabungT$T2))
GabungT[Tinggi3, ]
```
Baris mana yang terpilih?

- A. `1 3 4 6`
- B. `2 4 5 7 8 9 10`
- C. `4 5 8 9 10`
- D. `2 4 5 8 9 10`

**Jawaban: D**
- Kelompok L: butuh `T1 > 100` dan `T4 < 150`. Hanya baris **4** (T1 = 110, T4 = 138). Baris 1 dan 3 gagal karena T1 = 100 (bukan lebih dari 100), baris 6 gagal karena T4 = 150 (bukan kurang dari 150).
- Kelompok P: semua yang T2-nya tidak `NA`, yaitu 2, 5, 8, 9, 10. Baris **7** gugur karena T2 `NA`.

---

### Soal 22
```r
mean(GabungT$T2)
```
- A. `124.8889`
- B. `NA`
- C. `0`
- D. Error

**Jawaban: B**
Ada satu `NA` di `T2` (baris 7). Fungsi seperti `mean`, `sum`, `max` mengembalikan `NA` bila data mengandung `NA`. Dengan `mean(GabungT$T2, na.rm = TRUE)` hasilnya `124.8889` (jumlah 1124 dibagi 9).

---

### Soal 23
```r
order(GabungT$JK, decreasing = TRUE)
```
- A. `1 3 4 6 2 5 7 8 9 10`
- B. `10 9 8 7 5 2 6 4 3 1`
- C. `2 5 7 8 9 10 1 3 4 6`
- D. `6 4 3 1 10 9 8 7 5 2`

**Jawaban: C**
Dengan `decreasing = TRUE`, `"P"` ditaruh sebelum `"L"`. Di dalam kelompok yang sama, urutan asli **dipertahankan** (bukan dibalik): P di baris 2, 5, 7, 8, 9, 10 lalu L di baris 1, 3, 4, 6.

---

### Soal 24
```r
cut(c(110, 111, 150, 151),
    breaks = c(-Inf, 110, 150, Inf),
    labels = c("Pendek", "Sedang", "Tinggi"))
```
- A. `Pendek Sedang Sedang Tinggi`
- B. `Pendek Sedang Tinggi Tinggi`
- C. `Sedang Sedang Tinggi Tinggi`
- D. `Pendek Pendek Sedang Tinggi`

**Jawaban: A**
`cut()` membuat interval **tutup kanan** `(−Inf, 110]`, `(110, 150]`, `(150, Inf)`. Jadi 110 → Pendek, 111 dan **150 → Sedang**, 151 → Tinggi. Kalau ingin `[a, b)` pakai `right = FALSE`.

---

### Soal 25
```r
df <- data.frame(nilai = c(80, 55, 92, 67, 74))
cut(df$nilai, breaks = c(0, 60, 75, 100), labels = c("C", "B", "A"))
```
- A. `A C A B A`
- B. `A C A C B`
- C. `A B A B B`
- D. `A C A B B`

**Jawaban: D**
Interval: `(0,60]` = C, `(60,75]` = B, `(75,100]` = A.
80 → A, 55 → C, 92 → A, 67 → B, **74 → B** (74 ≤ 75). Output berupa factor dengan `Levels: C B A`.

---

### Soal 26 (merge)
```r
# GabungT.new1: 10 baris, kolom kunci Kodebaru = B1, B2, ..., B10
# databaru    : 3 baris, kolom kunci kode = "B1", "B3", "B.11"
merge(GabungT.new1, databaru, by.x = 1, by.y = 3, all = FALSE)
merge(GabungT.new1, databaru, by.x = 1, by.y = 3, all = TRUE)
merge(GabungT.new1, databaru, by.x = 1, by.y = 3, all.x = TRUE)
merge(GabungT.new1, databaru, by.x = 1, by.y = 3, all.y = TRUE)
```
Jumlah baris keempat hasil `merge` secara berurutan:

- A. `2`, `11`, `10`, `3`
- B. `2`, `13`, `10`, `3`
- C. `3`, `11`, `10`, `2`
- D. `2`, `10`, `10`, `3`

**Jawaban: A**
- `all = FALSE`: hanya yang cocok → B1 dan B3 = **2**.
- `all = TRUE`: 10 baris utama + `B.11` yang tak punya pasangan = **11**.
- `all.x = TRUE`: semua baris tabel pertama = **10**.
- `all.y = TRUE`: semua baris tabel kedua = **3** (B1, B3, B.11).

Perhatikan `"B.11"` tidak sama dengan `"B11"`. Baris yang tak cocok diisi `NA`.

---

### Soal 27 (reshape)
```r
# data1 (long): 12 baris, kolom Subjek, JK, Kondisi, Tekanan
# 4 subjek x 3 kondisi (kontrol, kondisi1, kondisi2)
wide = reshape(data1, idvar = c("Subjek", "JK"),
               timevar = "Kondisi", direction = "wide")
dim(wide)
```
- A. `12 4`
- B. `4 4`
- C. `4 5`
- D. `3 5`

**Jawaban: C**
Long → wide: tiap subjek menjadi 1 baris (**4 baris**). Kolomnya: `Subjek`, `JK` (id) + satu kolom `Tekanan.<kondisi>` untuk tiap level `timevar` (3 kolom) = **5 kolom**: `Subjek, JK, Tekanan.kontrol, Tekanan.kondisi1, Tekanan.kondisi2`. Nama baris diambil dari baris pertama tiap subjek di data long: `1, 4, 7, 10`.

---

### Soal 28 (nested ifelse)
```r
math <- c(61, 57, 52)
NH <- ifelse(math >= 80, "A",
      ifelse(math >= 75, "AB",
      ifelse(math >= 70, "B",
      ifelse(math >= 65, "BC",
      ifelse(math >= 60, "C",
      ifelse(math >= 55, "D", "E"))))))
Status <- ifelse(NH %in% c("D", "E"), "Tidak Lulus", "Lulus")
```
- A. NH: `BC D E`; Status: `Lulus`, `Tidak Lulus`, `Tidak Lulus`
- B. NH: `C D E`; Status: `Lulus`, `Tidak Lulus`, `Tidak Lulus`
- C. NH: `C D E`; Status: `Lulus`, `Lulus`, `Tidak Lulus`
- D. NH: `C E E`; Status: `Lulus`, `Tidak Lulus`, `Tidak Lulus`

**Jawaban: B**
61 ≥ 60 dan < 65 → `C`. 57 ≥ 55 dan < 60 → `D`. 52 < 55 → `E`. `D` dan `E` ada di `c("D","E")`, jadi status "Tidak Lulus"; hanya 61 yang "Lulus". `ifelse` diperiksa dari kondisi paling atas, urutan kondisi menentukan hasil.

---

# Bagian 4 — Visualisasi Data

## Materi inti

- **`plot(x, y, type = ...)`**: `"p"` titik, `"l"` garis, `"b"` titik+garis **dengan celah**, `"o"` titik ditimpa garis (*overplotted*, tanpa celah), `"h"` garis vertikal ke y = 0 (histogram-like), `"s"` tangga (*step*), `"n"` tidak menggambar apa pun (kerangka saja).
- **`par(mfrow = c(baris, kolom))`** membagi layar plot.
- **`pairs()`** = matriks scatterplot untuk semua pasangan variabel.
- **`boxplot(y ~ g)`** satu kotak per grup. `y ~ a*b` satu kotak per kombinasi.
- **`barplot(matriks)`** default **bertumpuk** (satu batang per kolom). `beside = TRUE` → berdampingan (satu batang per sel).
- **`hist(x, freq = FALSE)`** → sumbu y = density, supaya bisa ditumpuk dengan `density()`.
- Penambah elemen: `legend()`, `text()` (dalam plot), `mtext()` (tepi plot), `lines()`, `points()`, `abline()`. Ukuran font: `cex.main`, `cex.lab`.
- **ggplot2**: komponen = data, `aes()`, geoms, facets, stats, scales, coordinate system. Ditambah dengan `+`.
  - `geom_bar()` default `stat = "count"`. Bila y sudah dihitung sendiri pakai `stat = "identity"`.
  - Pie chart = `geom_bar()` + `coord_polar("y")` (tidak ada `geom_pie`).
- **3D**: `outer(x, y, f)` membuat matriks nilai z; `persp()` (permukaan 3D), `contour()`, `expand.grid`, `cloud()` (lattice).

## Soal

### Soal 29
Pasangan `type` pada `plot()` dan hasilnya yang **benar**:

- A. `"b"`: titik dan garis tersambung **tanpa celah**
- B. `"s"`: hanya titik
- C. `"n"`: garis putus-putus
- D. `"h"`: garis vertikal dari tiap titik ke y = 0

**Jawaban: D**
`"b"` punya **celah** antara titik dan garis (tanpa celah adalah `"o"`). `"s"` adalah tangga. `"n"` tidak menggambar isi sama sekali.

---

### Soal 30
```r
par(mfrow = c(3, 3))
type = c("p", "l", "b", "o", "h", "s", "n")
for (i in type) {
  plot(x, y, type = i, main = paste("type = ", i))
}
```
Apa yang tampil?

- A. 9 plot
- B. 7 plot (yang `"n"` hanya kerangka dan sumbu), 2 slot kosong
- C. 6 plot, karena `"n"` tidak dibuat
- D. Error

**Jawaban: B**
Ada 7 nilai `type`, jadi 7 kali `plot()`. `type = "n"` tetap membuat kerangka (sumbu dan judul) tanpa titik. Kisi 3×3 punya 9 slot, sisa **2 slot kosong**.

---

### Soal 31
```r
pairs(iris, lower.panel = NULL)
```
Berapa panel scatterplot di segitiga atas (tidak termasuk diagonal)?

- A. 4
- B. 6
- C. 10
- D. 20

**Jawaban: C**
`iris` memiliki **5** kolom (4 numerik + `Species`), sehingga matriksnya 5×5. Segitiga atas berisi `5×4/2 = 10` panel. Diagonal berisi nama variabel. (Subset `iris[, 1:4]` akan memberi 6 panel.)

---

### Soal 32
```r
boxplot(len ~ supp * dose, data = ToothGrowth)
```
Berapa kotak yang tampil? (`supp` punya 2 level, `dose` punya 3 nilai)

- A. 2
- B. 3
- C. 5
- D. 6

**Jawaban: D**
`supp * dose` membuat satu kotak per **kombinasi** grup: 2 × 3 = 6. Bandingkan: `boxplot(x)` = 1 kotak, `boxplot(x ~ Species)` = 1 kotak per spesies (3 untuk iris).

---

### Soal 33
`VADeaths` berukuran 5 baris × 4 kolom. Berapa batang yang tampil pada `barplot(VADeaths)` dan `barplot(VADeaths, beside = TRUE)`?

- A. 4 dan 20
- B. 5 dan 20
- C. 4 dan 5
- D. 20 dan 20

**Jawaban: A**
Default `barplot` pada matriks membuat **satu batang per kolom** yang **bertumpuk**: 4 batang (masing-masing 5 segmen). Dengan `beside = TRUE`, tiap sel menjadi batang sendiri: 5 × 4 = **20 batang**.

---

### Soal 34
```r
hist(trees$Height, freq = FALSE)
polygon(density(trees$Height), border = "red")
```
Mengapa perlu `freq = FALSE`?

- A. Agar histogram berwarna
- B. Agar sumbu x berupa persen
- C. Agar histogram tidak bisa ditumpuk dengan garis
- D. Agar sumbu y berupa *density* (luas total 1), sehingga skalanya sama dengan kurva `density()`

**Jawaban: D**
Default `hist` menampilkan **frekuensi**, sedangkan `density()` menghasilkan **kepadatan** (luas 1). Tanpa `freq = FALSE`, kurva akan terlihat rata dan tidak sebanding dengan histogram.

---

### Soal 35
```r
gapminder %>%
  filter(year == 1952 | year == 2007) %>%
  group_by(continent, year) %>%
  summarize(mean_lifeExp = mean(lifeExp)) %>%
  ggplot(aes(continent, mean_lifeExp, fill = factor(year))) +
  geom_bar(stat = "identity", position = position_dodge())
```
Berapa batang yang tampil, dan mengapa perlu `stat = "identity"`?

- A. 10 batang; karena `geom_bar()` default menghitung jumlah baris (`stat = "count"`), sedangkan di sini tinggi batang sudah berupa nilai `mean_lifeExp`
- B. 5 batang; karena `year` diabaikan
- C. 12 batang; karena ada 12 tahun
- D. 10 batang; karena `stat = "identity"` menghitung rata-rata otomatis

**Jawaban: A**
5 benua × 2 tahun = **10 batang** (berdampingan karena `position_dodge()`). `stat = "identity"` berarti "pakai nilai y apa adanya", bukan menghitung kemunculan. Opsi D salah: rata-rata dihitung oleh `summarize()`, bukan oleh `stat`.

---

### Soal 36
Cara membuat pie chart di `ggplot2`:

- A. `geom_pie()`
- B. `geom_circle()`
- C. `geom_bar(stat = "identity")` ditambah `coord_polar("y")`
- D. `geom_area()` ditambah `coord_flip()`

**Jawaban: C**
Tidak ada `geom_pie()`. Pie chart adalah **bar chart bertumpuk** yang digambar pada **sistem koordinat polar**. Kodenya: `ggplot(data, aes(x = "", y = persen, fill = kategori)) + geom_bar(stat = "identity") + coord_polar("y")`.

---

### Soal 37
```r
f <- function(x, y) (1/(2*pi)) * exp(-.5 * (x^2 + y^2))
y <- x <- seq(-3, 3, length = 50)
z <- outer(x, y, f)
dim(z)
```
Dimensi `z` dan jenis plot jika dipakai di `persp(x, y, z)`:

- A. `50 1`; plot garis
- B. `50 50`; plot permukaan 3D berbentuk lonceng (normal bivariat)
- C. `2500 1`; scatterplot
- D. `50 50`; boxplot

**Jawaban: B**
`outer(x, y, f)` menghitung `f` untuk **semua pasangan** (x, y): matriks 50 × 50. `persp()` menggambar matriks itu sebagai permukaan 3D. Nilai maksimumnya sedikit di bawah `1/(2π) = 0.159` karena grid tidak memuat titik 0 persis (≈ 0.1586).

---

# Bagian 5 & 6 — Pembangkitan Bilangan Acak

## Materi inti

- **Pseudorandom**: bilangan dari algoritma deterministik yang tampak acak. `set.seed()` membuat hasil **dapat direproduksi**.
- **Awalan fungsi distribusi**: `d` = pmf/pdf, `p` = CDF (peluang kumulatif `P(X ≤ x)`), `q` = kuantil (invers CDF), `r` = pembangkit acak. Contoh: `dbinom`, `pbinom`, `qbinom`, `rbinom`.
- **Argumen `rnorm(n, mean, sd)`**: argumen ketiga adalah **simpangan baku**, bukan ragam.
- **Peluang binomial**:
  - `P(X = k)` = `dbinom(k, n, p)`
  - `P(X ≤ k)` = `pbinom(k, n, p)`
  - `P(X > k)` = `pbinom(k, n, p, lower.tail = FALSE)` = `1 - pbinom(k, ...)`
  - `P(X ≥ k)` = `1 - pbinom(k-1, ...)`  ← jebakan "paling sedikit"
  - `P(a ≤ X ≤ b)` = `pbinom(b, ...) - pbinom(a-1, ...)`
- **Rumus yang perlu dihafal**:

| Distribusi | Rata-rata | Ragam |
|---|---|---|
| Uniform(a, b) | (a+b)/2 | (b−a)²/12 |
| Binomial(n, p) | np | np(1−p) |
| Beta(a, b) | a/(a+b) | — |
| t(ν) | 0 (ν > 1) | ν/(ν−2), hanya jika **ν > 2** |

- **Inverse transform**: cari `F(x)`, set `F(x) = u`, selesaikan untuk `x`, lalu `x = F⁻¹(U)` dengan `U ~ Uniform(0,1)`. Untuk kasus diskret: bandingkan `u` dengan batas kumulatif.
- **Acceptance-rejection**: bangkitkan `x ~ g`, `u ~ U(0,1)`, terima jika `u ≤ f(x)/(c·g(x))`. Peluang diterima = `1/c`, sehingga untuk `n` sampel dibutuhkan kira-kira **`c·n` iterasi**.
- **Metode transformasi**:

| Transformasi | Hasil |
|---|---|
| jumlah kuadrat k peubah N(0,1) | χ²(k) |
| `(U/d1)/(V/d2)`, U~χ²(d1), V~χ²(d2) | F(d1, d2) |
| `Z / sqrt(Y/k)`, Z~N(0,1), Y~χ²(k) | t(k) |
| `u/(u+v)`, u~Gamma(a,1), v~Gamma(b,1) | Beta(a, b) |
| Box-Muller | N(0,1) |

- **Simulasi regresi**: `Y = b0 + b1·X + error`; penduga OLS **tak bias**, rata-rata penduga ≈ nilai parameter, dan `sd` penduga mengecil bila n membesar.

## Soal

### Soal 38
```r
set.seed(1); a <- runif(3)
set.seed(1); b <- runif(3)
identical(a, b)
```
- A. `FALSE`
- B. `NA`
- C. `TRUE`
- D. Error

**Jawaban: C**
Seed yang sama menghasilkan urutan bilangan yang sama, sehingga hasil simulasi bisa direproduksi.

---

### Soal 39
```r
x <- runif(100)
Y <- (10 - 3) * x + 3
```
Distribusi `Y` beserta rata-rata dan ragam teoritisnya:

- A. `U(3, 10)`, rata-rata 6.5, ragam 49/12 ≈ 4.08
- B. `U(0, 7)`, rata-rata 3.5, ragam 49/12
- C. `U(3, 10)`, rata-rata 6.5, ragam 49
- D. `N(6.5, 7)`

**Jawaban: A**
`(b − a)·U + a` memetakan U(0,1) ke U(a, b) = U(3, 10). Rata-rata = (3+10)/2 = 6.5; ragam = (10−3)²/12 = 49/12. Hasil `range(Y)` ada di dalam (3, 10), `length(Y)` pasti 100.

---

### Soal 40
Pernyataan yang **benar**:

- A. `dbinom` adalah fungsi sebaran kumulatif (CDF) binomial
- B. `pnorm` adalah fungsi kuantil normal
- C. `rpois` adalah fungsi kepadatan Poisson
- D. `qnorm(0.975)` bernilai sekitar 1.96

**Jawaban: D**
`d` = pmf/pdf, `p` = CDF, `q` = kuantil, `r` = acak. `qnorm(0.975) = 1.959964`: nilai z yang memiliki luas 97,5% di sebelah kirinya.

---

### Soal 41
Misalkan `X ~ Binomial(5, 0.5)`. Nilai `dbinom(3, 5, 0.5)` dan `pbinom(3, 5, 0.5)` adalah:

- A. `0.3125` dan `0.9375`
- B. `0.3125` dan `0.8125`
- C. `0.8125` dan `0.3125`
- D. `0.3125` dan `0.1875`

**Jawaban: B**
`P(X = 3) = C(5,3)/32 = 10/32 = 0.3125`. `P(X ≤ 3) = (1+5+10+10)/32 = 26/32 = 0.8125`. Opsi D adalah `P(X > 3) = 6/32`. **Opsi A mengikuti kunci di file latihanmu yang salah** (lihat bagian Koreksi).

---

### Soal 42
`X ~ Binomial(10, 0.4)`. Ekspresi yang benar untuk `P(X ≥ 3)`:

- A. `1 - pbinom(2, 10, 0.4)`
- B. `1 - pbinom(3, 10, 0.4)`
- C. `pbinom(3, 10, 0.4)`
- D. `dbinom(3, 10, 0.4)`

**Jawaban: A**
`P(X ≥ 3) = 1 − P(X ≤ 2)`. Opsi B sebenarnya `P(X ≥ 4)`, C adalah `P(X ≤ 3)`, D hanya `P(X = 3)`. Hasil numeriknya ≈ 0.8327. Aturan: "paling sedikit k" → `1 - pbinom(k-1)`.

---

### Soal 43
Perusahaan memeriksa 20 produk, peluang lulus 0.7 (`n = 20`, `p = 0.7`). Peluang **10 sampai 15** produk lulus (inklusif):

- A. `pbinom(15, 20, 0.7) - pbinom(10, 20, 0.7)`
- B. `pbinom(14, 20, 0.7) - pbinom(9, 20, 0.7)`
- C. `dbinom(15, 20, 0.7) - dbinom(10, 20, 0.7)`
- D. `pbinom(15, 20, 0.7) - pbinom(9, 20, 0.7)`

**Jawaban: D**
`P(10 ≤ X ≤ 15) = P(X ≤ 15) − P(X ≤ 9)`. Kurangkan `pbinom(9)`, bukan `pbinom(10)`, agar 10 ikut terhitung. Nilainya ≈ 0.7453. (Sebagai pembanding: `1 - pbinom(14, 20, 0.7)` = P(X ≥ 15) ≈ 0.4164 dan `dbinom(12, 20, 0.7)` ≈ 0.1144.)

---

### Soal 44 (inverse transform kontinu)
Densitas `f(x) = 3x²`, `0 < x < 1`. Cara membangkitkan `x` dari `u ~ U(0,1)`:

- A. `x <- u^3`
- B. `x <- u^(1/3)`
- C. `x <- sqrt(u)`
- D. `x <- 3 * u^2`

**Jawaban: B**
`F(x) = ∫₀ˣ 3t² dt = x³`. Set `x³ = u` → `x = u^(1/3)`. Histogram hasilnya naik melengkung ke kanan, mengikuti kurva `3x²`.

---

### Soal 45 (inverse transform diskret)
```r
u <- runif(1000)
x <- ifelse(u <= 0.5, 0, ifelse(u <= 0.8, 1, 2))
table(x)
```
Peluang yang digunakan adalah `P(0) = 0.5`, `P(1) = 0.3`, `P(2) = 0.2`. Perkiraan hasil `table(x)`:

- A. `0`: ±200, `1`: ±300, `2`: ±500
- B. `0`: ±333, `1`: ±333, `2`: ±333
- C. `0`: ±500, `1`: ±300, `2`: ±200
- D. `0`: ±500, `1`: ±800, `2`: ±1000

**Jawaban: C**
Batas kumulatif `F(0) = 0.5`, `F(1) = 0.8`, `F(2) = 1`. Selang `u ≤ 0.5` (peluang 0.5) menjadi 0, `0.5 < u ≤ 0.8` (peluang 0.3) menjadi 1, sisanya (0.2) menjadi 2. Jumlah total pasti 1000, dan angka persisnya acak. Opsi D salah karena menjumlah ke lebih dari 1000.

---

### Soal 46 (acceptance-rejection)
```r
n <- 1000; k <- 0; j <- 0
y <- numeric(n)
while (k < n) {
  u <- runif(1)
  j <- j + 1
  x <- runif(1)
  if (x * (1 - x) > u) {
    k <- k + 1
    y[k] <- x
  }
}
j
```
Kode ini membangkitkan Beta(2,2) dengan `f(x) = 6x(1−x)`, proposal `U(0,1)`, dan `c = 6`. Perkiraan nilai `j`:

- A. ≈ 6000
- B. ≈ 1000
- C. ≈ 167
- D. ≈ 600

**Jawaban: A**
Syarat terima `u ≤ f(x)/(c·g(x)) = 6x(1−x)/6 = x(1−x)`. Peluang diterima = `1/c = 1/6`, sehingga untuk 1000 sampel dibutuhkan ≈ `6 × 1000 = 6000` iterasi. `j` = jumlah **semua percobaan** (termasuk yang ditolak), `k` = jumlah sampel **yang diterima**. Nilai persisnya acak (di slide: 5813).

---

### Soal 47 (transformasi: Gamma → Beta)
```r
u <- rgamma(1000, shape = 3, rate = 1)
v <- rgamma(1000, shape = 2, rate = 1)
x <- u / (u + v)
```
Distribusi `x` dan nilai `mean(x)`:

- A. `Gamma(5, 1)`, rata-rata ≈ 5
- B. `Beta(2, 3)`, rata-rata ≈ 0.4
- C. `Beta(3, 2)`, rata-rata ≈ 0.4
- D. `Beta(3, 2)`, rata-rata ≈ 0.6

**Jawaban: D**
`u/(u+v)` dengan `u ~ Gamma(a,1)` dan `v ~ Gamma(b,1)` berdistribusi **Beta(a, b)** = Beta(3, 2). Rata-ratanya `a/(a+b) = 3/5 = 0.6`. Nilai `x` selalu di antara 0 dan 1.

---

### Soal 48 (t-student lewat transformasi)
```r
z <- rnorm(1000)
y <- rchisq(1000, df = 2)
x <- z / sqrt(y / 2)
```
- A. `x ~ t(2)`; `var(x)` tidak stabil karena ragam teoritisnya tak hingga
- B. `x ~ N(0, 1)`; `var(x)` ≈ 1
- C. `x ~ F(1, 2)`; `var(x)` ≈ 2
- D. `x ~ t(2)`; `var(x)` ≈ 2 karena `ν/(ν−2)`

**Jawaban: A**
`Z/sqrt(Y/k)` dengan `Y ~ χ²(k)` adalah **t(k)**, di sini t(2). Ragam t(ν) = `ν/(ν−2)` hanya ada jika `ν > 2`. Untuk `ν = 2` penyebutnya 0 → ragam **tak hingga**, sehingga `var(x)` melonjak antar sampel. Histogramnya simetris di sekitar 0 dengan ekor lebih tebal dari normal.

---

### Soal 49
```r
sample(1:5, size = 10)
```
- A. 10 bilangan acak dari 1 sampai 5 (dengan pengembalian)
- B. 5 bilangan saja
- C. Error: `cannot take a sample larger than the population when 'replace = FALSE'`
- D. Warning lalu menghasilkan 10 bilangan

**Jawaban: C**
Default `sample()` adalah **tanpa pengembalian** (`replace = FALSE`), jadi tidak bisa mengambil 10 dari populasi 5. Perbaikan: `sample(1:5, 10, replace = TRUE)`.

---

### Soal 50 (regresi: jebakan typo)
```r
n <- 1000
set.seed(12)
epsilon <- rnorm(n, mean = 0, sd = 1)
set.seed(10)
X <- replicate(2, rnorm(n, mean = 0, sd = 1.5))
bbetas <- matrix(c(5, 1, 2), nrow = 3, ncol = 1)
Xgab <- cbind(1, X)
Y <- Xgab %*% betas + epsilon
```
- A. `Y` berupa matriks 1000 × 1
- B. `Error: object 'betas' not found`
- C. Warning: dimensi tidak cocok
- D. `Y` berupa vektor panjang 1000

**Jawaban: B**
Objek yang dibuat bernama `bbetas`, tetapi yang dipakai di perkalian matriks `betas`. R tidak menemukannya, sehingga error. Kalau typo diperbaiki, maka: `dim(X)` = `1000 2`, `dim(Xgab)` = `1000 3`, `dim(Y)` = `1000 1`, dan `lm(Y ~ .)` memberi koefisien ≈ `5, 1, 2` (residual standard error ≈ 1).

---

### Soal 51 (simulasi regresi sederhana)
```r
for (i in 1:100) {
  error <- rnorm(10); X <- rnorm(10)
  Y <- 1 + 1*X + error
  obj <- lm(Y ~ X)
  b0topi <- c(b0topi, obj$coefficients[1])
  b1topi <- c(b1topi, obj$coefficients[2])
}
```
Nilai `mean(b0topi)` dan `mean(b1topi)`, serta alasannya:

- A. ≈ 0 dan ≈ 0, karena error berata-rata 0
- B. ≈ 1 dan ≈ 1, karena n = 10
- C. Tidak dapat diperkirakan karena acak
- D. ≈ 1 dan ≈ 1, karena penduga OLS **tak bias** (rata-rata penduga ≈ parameter sebenarnya)

**Jawaban: D**
Parameter yang dipakai `β0 = 1` dan `β1 = 1`. Karena `E(β̂) = β`, rata-rata dari 100 pendugaan mendekati 1. Panjang `b0topi` dan `b1topi` masing-masing **100**. Menaikkan `rnorm(10)` menjadi `rnorm(100)` membuat `sd` penduga **mengecil**, tetapi rata-ratanya tetap ≈ 1.

---

# Bagian 7 — Fungsi & OOP (S3/S4)

## Materi inti

- **Fungsi**: `nama <- function(arg1, arg2 = default) { ...; return(hasil) }`. Tanpa `return()`, R mengembalikan nilai **ekspresi terakhir**. Jika ekspresi terakhir berupa assignment, hasilnya dikembalikan secara *invisible* (tidak tercetak, tetapi tetap bisa disimpan).
- **Default argument**: argumen yang tak diisi (atau dikosongkan seperti `f(, 9)`) memakai nilai default. Argumen bernama boleh diberikan dengan urutan berbeda.
- **Error umum**: argumen wajib tak diisi → `argument "x" is missing, with no default`; argumen kelebihan → `unused argument`.
- **Rekursi**: fungsi memanggil dirinya sendiri; wajib punya **kondisi henti** yang pasti tercapai (jika tidak → infinite recursion).
- **S3**: objek = `list` + `class(x) <- "nama"`. Konstruktor = fungsi pembuat objek (bisa validasi dengan `stop()`). Method = `nama.kelas`, misalnya `print.mahasiswa`. Fungsi generik baru: `f <- function(obj) UseMethod("f")`. Akses elemen dengan `$`. Tidak ada validasi setelah objek dibuat.
- **S4**: `setClass("nama", slots = list(...))`, `new("nama", ...)`, akses slot dengan `@` atau `slot(obj, "slot")`. Tipe slot **divalidasi otomatis**. Tampilan: `setMethod("show", "nama", function(object) ...)`. Generik baru: `setGeneric("f", function(obj) standardGeneric("f"))` lalu `setMethod("f", "nama", ...)`.

## Soal

### Soal 52
```r
jumlah <- function(x = 8, y = 8) {
  return(x + y)
}
jumlah(, 9)
jumlah(y = 1)
jumlah(10)
```
- A. `17`, `1`, `10`
- B. `17`, `9`, `18`
- C. `9`, `9`, `18`
- D. Error, `9`, `18`

**Jawaban: B**
`jumlah(, 9)`: `x` dikosongkan → default 8, `8 + 9 = 17`. `jumlah(y = 1)`: `x` default 8, `8 + 1 = 9`. `jumlah(10)`: `x = 10`, `y` default 8, jadi 18.

---

### Soal 53
```r
h <- function(x) {
  y <- x * 2
}
h(3)
z <- h(3)
z
```
- A. Mencetak `[1] 6`, lalu `[1] 6`
- B. Error karena tidak ada `return()`
- C. `h(3)` tidak mencetak apa-apa, `z` bernilai `6`
- D. `h(3)` mencetak `NULL`, `z` bernilai `NULL`

**Jawaban: C**
Fungsi tanpa `return()` tetap mengembalikan nilai ekspresi terakhir. Ekspresi terakhir `y <- x * 2` adalah assignment, sehingga nilainya dikembalikan secara *invisible* dan tidak tercetak, tetapi tetap tersimpan saat di-assign ke `z` (= 6). Jika baris terakhirnya `y + 1`, hasilnya akan tercetak.

---

### Soal 54 (rekursi)
```r
jum <- function(n) {
  if (n == 1) {
    return(1)
  } else {
    return(n + jum(n - 1))
  }
}
jum(5)
```
- A. `5`
- B. `10`
- C. `120`
- D. `15`

**Jawaban: D**
`jum(5) = 5 + jum(4) = 5 + 4 + jum(3) = ... = 5+4+3+2+1 = 15`. Fungsi ini menjumlahkan bilangan asli 1 sampai n. (Faktorial akan memberi 120, yaitu opsi C, dengan `*` bukan `+`.)

---

### Soal 55
Pada fungsi `jum` di Soal 54, apa yang terjadi jika memanggil `jum(0)`?

- A. Rekursi tak hingga, R berhenti dengan error (mis. `evaluation nested too deeply: infinite recursion`)
- B. Menghasilkan `0`
- C. Menghasilkan `1`
- D. Menghasilkan `NULL`

**Jawaban: A**
`jum(0)` memanggil `jum(-1)`, `jum(-2)`, ..., dan kondisi `n == 1` **tidak pernah tercapai**. Perbaikan: kondisi henti `if (n <= 1)`.

---

### Soal 56
```r
root <- function(a, b, c) {
  D <- b^2 - 4*a*c
  if (D < 0) {
    return("Tidak ada akar real")
  }
  c((-b + sqrt(D)) / (2*a), (-b - sqrt(D)) / (2*a))
}
root(1, -3, 2)
```
- A. `[1] 1 2`
- B. `[1] 2 1`
- C. `[1] -1 -2`
- D. `[1] "Tidak ada akar real"`

**Jawaban: B**
`D = 9 − 8 = 1`. Akar pertama `(3 + 1)/2 = 2`, akar kedua `(3 − 1)/2 = 1`. Urutan hasil mengikuti urutan penulisan dalam `c()`. (Latihan lain: `faktorial(5)` = 120, `kombinasi(5, 2)` = 10.)

---

### Soal 57 (S3: tampilan default)
```r
m <- list(nama = "andi", umur = 19, GPA = 3.2)
class(m) <- "mahasiswa itera"
m
```
- A. Error karena nama kelas memakai spasi
- B. `[1] "mahasiswa itera"`
- C. Hanya `$nama` saja
- D. Seluruh isi list (`$nama`, `$umur`, `$GPA`) lalu `attr(,"class")` berisi `"mahasiswa itera"`

**Jawaban: D**
Objek S3 hanyalah **list dengan atribut class**. Karena belum ada method `print` untuk kelas itu, R memakai tampilan list biasa dan menambahkan `attr(,"class")`. `class(m)` memberi `"mahasiswa itera"` dan `unclass(m)$nama` memberi `"andi"`.

---

### Soal 58 (S3: konstruktor)
```r
mhs <- function(n, u, g) {
  if (g > 4 || g < 0) {
    stop("GPA harus berada pada rentang 0 sampai 4")
  }
  mahasiswa <- list(nama = n, umur = u, gpa = g)
  class(mahasiswa) <- "mhsitera"
  mahasiswa
}
m <- mhs("Jakob", 19, 3)
m <- mhs("Ingrid", 24, 4.5)
m$nama
```
- A. `[1] "Jakob"` (setelah error di baris ke-2 pemanggilan)
- B. `[1] "Ingrid"`
- C. `NULL`
- D. Error: objek `m` tidak ditemukan

**Jawaban: A**
Pemanggilan kedua gagal oleh `stop()`, jadi assignment ke `m` **tidak terjadi** dan `m` tetap berisi objek pertama (Jakob, 19, 3). Pesan error: `GPA harus berada pada rentang 0 sampai 4`.

---

### Soal 59
Setelah `m <- mhs("Jakob", 19, 3)` (S3, soal 58), dijalankan `m$gpa <- 8`. Hasilnya:

- A. Error dari `stop()` pada konstruktor
- B. Error karena objek S3 tidak boleh diubah
- C. Berhasil tanpa error, `m$gpa` menjadi 8
- D. Warning lalu nilai tetap 3

**Jawaban: C**
Validasi hanya dijalankan **saat konstruktor dipanggil**. Setelah objek jadi, S3 tidak punya penjaga tipe/nilai. Inilah kelemahan S3 dibanding S4.

---

### Soal 60 (S3: nama method)
```r
mhs <- function(n, u, g) {
  mahasiswa <- list(nama = n, umur = u, gpa = g)
  class(mahasiswa) <- "mhsitera"
  mahasiswa
}
print.mahasiswa <- function(obj) {
  cat(obj$nama, "\n")
}
m <- mhs("Jakob", 19, 3)
m
```
- A. `Jakob`
- B. Tampilan list default (`$nama`, `$umur`, `$gpa`, `attr(,"class")`)
- C. Error: method tidak ditemukan
- D. `NULL`

**Jawaban: B**
Kelas objek `"mhsitera"`, sedangkan method didefinisikan untuk `print.mahasiswa`. Nama method harus persis `print.<kelas>`. Karena tidak cocok, R memakai `print` default (tampilan list), **bukan error**. Jika kelasnya `"mahasiswa"`, output-nya `Jakob ` (ada spasi sebelum newline karena `cat(..., "\n")`).

---

### Soal 61 (S3: fungsi generik)
```r
grade <- function(obj) {
  UseMethod("grade")
}
grade.mahasiswa <- function(obj) {
  cat("IPK", obj$nama, "adalah", obj$gpa)
}
grade(5)
```
- A. `IPK 5 adalah`
- B. `NULL`
- C. `[1] 5`
- D. Error: `no applicable method for 'grade' applied to an object of class "c('double', 'numeric')"`

**Jawaban: D**
`UseMethod("grade")` mencari `grade.<kelas>` sesuai kelas argumen. Untuk angka `5` (kelas `double`/`numeric`) tidak ada `grade.numeric` dan tidak ada `grade.default`, maka error. Bila memanggil `grade(mhs("Jakob", 19, 3))` dengan kelas `"mahasiswa"`, output-nya `IPK Jakob adalah 3`.

---

### Soal 62 (S4: validasi tipe)
```r
setClass("mahasiswa", slots = list(nama = "character", umur = "numeric", gpa = "numeric"))
new("mahasiswa", nama = "X", umur = "dua puluh", gpa = 3)
```
- A. Error validasi: slot `umur` seharusnya `numeric`, tetapi diberi `character`
- B. Objek terbentuk, `umur` disimpan sebagai teks
- C. Objek terbentuk, `umur` menjadi `NA`
- D. Warning saja

**Jawaban: A**
Slot S4 punya **tipe yang divalidasi otomatis**. Pesannya kurang lebih `invalid class “mahasiswa” object: invalid object for slot "umur" ... got class "character", should be or extend class "numeric"`. Inilah keunggulan S4 dibanding S3.

---

### Soal 63 (perbandingan S3 vs S4)
Pasangan pernyataan yang **benar**:

- A. S3 dibuat dengan `setClass()`; S4 dibuat dengan `class(x) <-`
- B. S3 diakses dengan `@`; S4 diakses dengan `$`
- C. S3: akses `obj$elemen` dan validasi manual; S4: akses `obj@slot` dan tipe slot divalidasi otomatis
- D. S3 memakai `setMethod("show", ...)` untuk tampilan; S4 memakai `print.kelas`

**Jawaban: C**

| Aspek | S3 | S4 |
|---|---|---|
| Membuat kelas | `class(x) <- "nama"` pada list | `setClass("nama", slots = ...)` |
| Membuat objek | konstruktor buatan sendiri | `new("nama", ...)` |
| Akses elemen | `obj$elemen` | `obj@slot` atau `slot(obj, "slot")` |
| Validasi tipe | tidak ada | otomatis pada slot |
| Tampilan | `print.kelas` | `setMethod("show", "kelas", ...)` |
| Generik baru | `UseMethod("f")` | `setGeneric()` + `setMethod()` |

---

### Soal 64 (S4: show)
Cara mengubah tampilan objek S4 ketika namanya diketik di console:

- A. `print.mahasiswa <- function(obj) ...`
- B. `setMethod("show", "mahasiswa", function(object) ...)`
- C. `setGeneric("print", ...)` tanpa `setMethod`
- D. `UseMethod("show")`

**Jawaban: B**
Pada S4, tampilan objek diatur lewat method **`show`**. Disarankan memakai nama argumen `object` agar sesuai generiknya. Contoh dari soal latihan: `s` (objek S4 dengan nama `"Jon Snow"`, umur 21) akan mencetak `Jon Snow` lalu `21`.

---

# Bagian 8 — Campuran (Latihan Akhir)

### Soal 65
```r
fib <- function(n) {
  if (n <= 2) return(1)
  fib(n - 1) + fib(n - 2)
}
sapply(1:8, fib)
```
- A. `1 2 3 5 8 13 21 34`
- B. `1 1 2 3 5 8 13 21`
- C. `0 1 1 2 3 5 8 13`
- D. `1 1 2 4 8 16 32 64`

**Jawaban: B**
`fib(1) = fib(2) = 1`, lalu tiap suku adalah jumlah dua suku sebelumnya. Deret Fibonacci dari 8 suku pertama. `sapply` menerapkan fungsi ke tiap elemen `1:8` dan menggabungkan hasil menjadi vektor.

---

### Soal 66
```r
m <- matrix(1:6, nrow = 2)
m %*% t(m)
apply(m, 1, sum)
```
- A. `[1,] 1 4 / [2,] 9 16` dan `9 12`
- B. `[1,] 35 44 / [2,] 44 56` dan `3 7 11`
- C. `[1,] 35 44 / [2,] 44 56` dan `9 12`
- D. Error karena ukuran tidak cocok

**Jawaban: C**
`m` = `[1 3 5; 2 4 6]` (2×3), `t(m)` berukuran 3×2, sehingga `m %*% t(m)` adalah matriks 2×2: `[1·1+3·3+5·5, 1·2+3·4+5·6; ..., 2·2+4·4+6·6] = [35 44; 44 56]`. `apply(m, 1, sum)` menjumlahkan per **baris** (`MARGIN = 1`): `1+3+5 = 9`, `2+4+6 = 12`. Opsi B salah di bagian kedua: `3 7 11` adalah jumlah per **kolom** (`MARGIN = 2`), sedangkan soal memakai `MARGIN = 1`. Opsi A adalah hasil perkalian per elemen (`m * m`-style), bukan perkalian matriks `%*%`.

---

### Soal 67
```r
x <- c(10, 20, 30)
names(x) <- c("a", "b", "c")
x["b"]
```
- A. `[1] 20`
- B. `[1] "b"`
- C. ` b ` (baris atas) dan `20` (baris bawah)
- D. `[1] 2`

**Jawaban: C**
Indeks dengan nama mengembalikan **vektor bernama**, jadi nama `b` tercetak di atas nilai `20`. Untuk hanya mendapat angka tanpa nama, pakai `unname(x["b"])` atau `x[["b"]]`.

---

### Soal 68
```r
df <- data.frame(a = 1:3, b = c("x", "y", "z"))
df$c <- df$a * 2
df[df$c > 2, ]
```
- A. Baris dengan `a = 2, 3`, nomor baris menjadi `1` dan `2`
- B. Baris dengan `a = 2, 3`, nomor baris tetap `2` dan `3`
- C. Seluruh baris
- D. Hanya kolom `c`

**Jawaban: B**
`c` bernilai `2, 4, 6`; yang `> 2` adalah baris 2 dan 3. Subsetting data frame **mempertahankan nomor baris asli** (2 dan 3), bukan direset.

---

# Lampiran A — Daftar Jebakan Singkat (Baca Malam Sebelum Kuis)

1. `paste0(..., sep = "-")` → `sep` ikut **digabung** ke tiap elemen.
2. `matrix()` terisi **per kolom**, kecuali `byrow = TRUE`.
3. `[ ]` pada list → list; `[[ ]]` → isinya.
4. `cut()` → interval **tutup kanan** `(a, b]`.
5. `order()` → indeks; `sort()` → nilai. `NA` di akhir; data kembar mempertahankan urutan asli.
6. `mean()` dengan `NA` → `NA`, kecuali `na.rm = TRUE`.
7. `merge()`: `all = FALSE` inner, `all.x` left, `all.y` right, `all = TRUE` full. Kunci tidak identik (`B.11` ≠ `B11`) → tidak cocok.
8. `pbinom(k)` = `P(X ≤ k)`. "Paling sedikit k" = `1 - pbinom(k-1)`. "Antara a dan b" = `pbinom(b) - pbinom(a-1)`.
9. `rnorm(n, mean, sd)` → argumen ketiga **sd**, bukan ragam.
10. `sample()` tanpa `replace = TRUE` tidak bisa mengambil lebih banyak dari populasi.
11. `cat()` tanpa `[1]` dan tanpa newline otomatis; `print()` dengan `[1]`.
12. Fungsi tanpa `return()` mengembalikan ekspresi terakhir; assignment terakhir tidak tercetak.
13. Rekursi tanpa kondisi henti yang tercapai → error infinite recursion.
14. S3: nama method harus `fungsi.kelas`; tidak ada validasi setelah objek dibuat. S4: `@`, `new()`, tipe slot divalidasi, tampilan lewat `show`.
15. Typo nama objek/fungsi (`betas`/`bbetas`, `rnom`) dan kolom data frame beda panjang → jawabannya **Error**.
16. Hasil acak: yang pasti hanya **panjang**, **rentang**, dan perkiraan rata-rata.

# Lampiran B — Koreksi untuk File Latihan Aslimu

1. **Soal 79 (kunci jawaban salah).** Di file latihan tertulis `pbinom(3, 5, 0.5) = 0.9375` dan `pbinom(..., lower.tail = FALSE) = 0.0625`. Yang benar: **`pbinom(3, 5, 0.5) = 0.8125`** dan **`P(X > 3) = 0.1875`**. (`P(X ≤ 3) = (1+5+10+10)/32 = 26/32`.) Nilai lain di soal itu sudah benar (`dbinom = 0.3125`, `qbinom(0.5, 5, 0.5) = 2`, `dnorm(0) = 0.3989`, `pnorm(0) = 0.5`, `qnorm(0.975) = 1.96`).
2. **Soal 117 (S4 `show`).** Kode slide memakai `function(obj)` pada `setMethod("show", ...)`. Generik `show` mendefinisikan argumennya sebagai `object`, dan nama yang berbeda dapat memicu error saat `setMethod`. Aman dan benar: pakai `function(object)`. Jika di soal ujian tertulis `obj` dan ada opsi "error", pertimbangkan opsi itu, atau ikuti apa yang kamu lihat berjalan di R saat latihan.

Semoga sukses kuisnya.
