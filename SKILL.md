---
name: "skill-web-wahyu"
description: "Gunakan saat user ingin membuat aplikasi web baru dengan PHP native monolith di Valet (MySQL/localStorage/SQLite, clean URL, modular, UI referensi Sassly)."
---

# skill-web-wahyu - Pembuat Aplikasi PHP Native Monolith

Skill ini membawa user dari nol sampai aplikasi jalan tanpa error:
wawancara parameter -> setup folder & Valet -> cek environment -> rencana -> coding -> verifikasi -> serah terima.

Ikuti tahap secara berurutan. JANGAN menulis kode aplikasi sebelum Tahap 1-4 selesai.
Bahasa komunikasi dengan user: Indonesia.

---

## Tahap 1 - Tanyakan parameter ke user

Jika user sudah menyebut sebagian info saat memanggil skill, jangan tanyakan ulang. Tanyakan hanya yang belum ada.

### 1a. Pertanyaan isian (kirim dalam SATU pesan, bernomor)
1. Nama aplikasi
2. Deskripsi: untuk apa, siapa penggunanya, masalah apa yang diselesaikan
3. Fitur utama (daftar, satu per baris)
4. Role pengguna dan hak aksesnya (default: satu role admin)
5. Catatan tambahan (opsional): format tanggal/angka, zona waktu, integrasi, batasan khusus

### 1b. Pertanyaan pilihan (pakai AskUserQuestion jika tersedia; jika tidak, tanyakan dalam teks)

Q1. Penyimpanan data - selalu jelaskan perbedaannya:
- localStorage (paling simpel dibagikan): tanpa database. Data tersimpan di browser masing-masing. Cocok untuk demo/prototipe yang mau langsung dijalankan orang lain cukup dengan double-click start script (syaratnya hanya PHP).
- MySQL (lebih proper): data terpusat, relasi antar tabel, multi-user, siap dipakai sungguhan. Perlu setup MySQL (kredensial di `.env`) dan migrasi.
- SQLite (tengah-tengah): data tersimpan di satu file, tanpa server database, bisa dibawa ke PC lain bersama folder proyek.
Jika user belum tahu / ragu: rekomendasikan localStorage untuk demo atau aplikasi yang mau dikasih ke orang untuk langsung dijalankan, dan MySQL untuk aplikasi yang akan dipakai sungguhan dan dikembangkan terus.

Q2. Lokasi folder proyek:
- ~/Sites/{slug} (direkomendasikan)
- Folder lain (user sebutkan path-nya)
- Folder saat ini

Q3. HTTPS lokal dengan valet secure:
- Ya, pakai https://{slug}.test (direkomendasikan)
- Tidak, cukup http://{slug}.test

Q4. Referensi UI:
- Sassly (default, lihat Tahap 6)
- Referensi lain (user kirim link atau screenshot)

### 1c. Nilai turunan (isi otomatis, tampilkan untuk dikonfirmasi)
- SLUG: dari nama aplikasi, huruf kecil + tanda hubung, singkat (mis. "SiGudang Inventaris" -> sigudang)
- URL: http(s)://{slug}.test
- DB_NAME (mode MySQL): slug dengan underscore (mis. si_gudang)
- MySQL: host 127.0.0.1, port 3306, user `root`, password: tanyakan ke user atau baca variabel lingkungan `DB_PASSWORD`. Tulis password hanya di `.env` (jangan di chat, README, atau kode).
- Bahasa UI & komentar kode: Indonesia. Nama variabel, fungsi, kelas: bahasa Inggris
- Rencana selalu ditunjukkan dulu dan ditunggu persetujuannya, kecuali user bilang "langsung saja"

Tampilkan ringkasan semua parameter dalam satu tabel, minta konfirmasi, baru lanjut ke Tahap 2.

---

## Tahap 2 - Panduan setup folder & Valet (jelaskan ke user, lalu jalankan)

Tulis panduan ini ke user dengan nilai yang sudah terisi (slug, folder), jelaskan fungsi setiap perintah dalam satu kalimat, lalu jalankan satu per satu jika kamu punya akses terminal. Laporkan hasil setiap langkah.

### Langkah 1 - Cek Valet & PHP
```bash
valet --version     # Valet terpasang?
valet paths         # folder yang di-park (semua subfolder-nya otomatis jadi .test)
valet links         # site yang sudah di-link
php -v              # versi PHP global yang dipakai Valet
```

### Langkah 2 - Pilih / buat folder proyek
```bash
mkdir -p ~/Sites/{slug}
cd ~/Sites/{slug}
```
Info: jika ~/Sites sudah di-park (muncul di `valet paths`), subfolder otomatis bisa diakses sebagai {nama-folder}.test. Untuk mem-park sebuah folder: `cd ~/Sites && valet park`. Tetap jalankan `valet link` di langkah berikut agar nama site eksplisit.

### Langkah 3 - valet link (daftarkan site)
```bash
cd ~/Sites/{slug}
valet link {slug}       # site tersedia di http://{slug}.test
valet links             # pastikan {slug} muncul di daftar
```
Valet otomatis melayani `public/index.php` sebagai document root, jadi site baru tampil setelah file itu dibuat. Clean URL langsung jalan karena Valet meneruskan semua request yang bukan file ke index.php.
Membatalkan: `valet unlink {slug}`.

### Langkah 4 - valet secure (HTTPS lokal, jika Q3 = Ya)
```bash
valet secure {slug}     # site pindah ke https://{slug}.test
```
- Valet membuat sertifikat TLS lokal dan memasukkannya ke Keychain sebagai trusted.
- Perintah ini butuh sudo, jadi password laptop akan diminta. Jika terminal agent tidak bisa menerima input password, minta user menjalankan perintah ini sendiri di Terminal lalu konfirmasi.
- Jika browser masih menolak sertifikat, tutup lalu buka ulang browser.
- Membatalkan: `valet unsecure {slug}`.
- Dampak ke kode: APP_URL=https://{slug}.test di .env, dan cookie session memakai flag Secure saat request HTTPS.

### Langkah 5 - Versi PHP per site (opsional)
Default: pakai PHP global terbaru. Jika proyek butuh versi tertentu:
```bash
valet isolate php@8.4 --site={slug}     # sesuaikan versi yang terpasang
valet unisolate --site={slug}
```

### Langkah 6 - Perintah Valet yang berguna
```bash
valet open {slug}       # buka site di browser
valet restart           # restart nginx/php-fpm jika ada yang aneh
valet log               # lihat daftar log (nginx, php-fpm)
```

### Langkah 7 - MySQL (hanya mode MySQL)
```bash
brew services list | grep -i -E "mysql|mariadb"    # service berjalan?
brew services start mysql                           # jika belum berjalan
mysql -u root -p -e "SELECT VERSION();"            # tes koneksi (password diminta)
```
Database dibuat oleh `php bin/migrate.php` (Tahap 5), bukan manual.

---

## Tahap 3 - Cek environment (wajib sebelum coding)

Jalankan dan laporkan dalam tabel singkat:
- `php -v` -> pakai fitur PHP terbaru SESUAI versi ini, jangan melebihi.
- `php -m` -> pastikan ada: pdo, pdo_mysql, pdo_sqlite, mbstring, json, fileinfo, openssl.
- `php --ini` -> php.ini yang aktif.
- `mysql --version` dan tes koneksi (mode MySQL).

Jika ada yang hilang (ekstensi, MySQL mati, Valet tidak terpasang): BERHENTI, jelaskan masalahnya, berikan perintah perbaikannya. Jangan menebak versi.

---

## Tahap 4 - Rencana (tunggu persetujuan)

Kirim rencana singkat:
1. Ringkasan pemahaman aplikasi (2-3 kalimat)
2. Daftar modul
3. Model data: tabel + kolom + tipe + relasi (MySQL/SQLite), atau key localStorage + bentuk objeknya (localStorage)
4. Daftar route: method, URL, fungsi
5. Design tokens dari referensi + peta komponen: kebutuhan aplikasi -> komponen Sassly yang dipakai, atau "dibuat sendiri"

Tunggu persetujuan user, kecuali user sudah bilang "langsung saja".

---

## Tahap 5 - Bangun aplikasi (aturan wajib)

### Stack
- PHP native (flat PHP) versi terbaru yang terpasang. `declare(strict_types=1);` di setiap file PHP.
- TANPA framework (Laravel, CodeIgniter, Slim, dll.), TANPA Composer package, TANPA npm/bundler/build step.
- Autoload dengan `spl_autoload_register()` buatan sendiri (gaya PSR-4).
- Monolith: PHP merender HTML di server. Tidak ada pemisahan frontend/backend, tidak ada SPA, tidak ada REST API terpisah. `fetch()` hanya untuk interaksi kecil (hapus tanpa reload, live search) ke route aplikasi yang sama.
- HTML5 semantik, CSS native, JavaScript native (ES modules, `<script type="module">`). DILARANG: jQuery, Bootstrap, Tailwind, React, Vue, Alpine, atau library UI lain.
- Semua aset (CSS, JS, ikon SVG, font) disimpan lokal, tanpa CDN, agar tetap jalan offline di PC lain.

### Mode data
A. MySQL
- PDO + prepared statement, charset utf8mb4, collation utf8mb4_unicode_ci.
- Kredensial di `.env` (parser sederhana buatan sendiri); sediakan `.env.example`; `.env` masuk `.gitignore`.
- `database/migrations/*.sql` berurutan (001_create_users.sql, dst.), `database/seeders/`, dan CLI:
  - `php bin/migrate.php` -> buat database jika belum ada + jalankan migrasi yang belum jalan
  - `php bin/seed.php` -> isi data contoh

B. localStorage
- PHP tetap menyajikan layout, halaman, dan clean URL. Data disimpan di browser lewat satu modul `public/assets/js/store.js` dengan fungsi jelas: `all()`, `find()`, `create()`, `update()`, `remove()`, `reset()`, `exportJson()`, `importJson()`.
- Isi data contoh saat localStorage masih kosong. Sediakan tombol "Reset data demo" serta "Export / Import data (JSON)" agar data bisa dipindah antar-PC.
- Bungkus akses localStorage dengan try/catch dan tampilkan pesan jika storage tidak tersedia.
- Tampilkan label kecil: "Mode demo - data hanya tersimpan di browser ini".
- Sediakan start script di root proyek agar orang lain bisa langsung menjalankan:
  - `start.command` (macOS, chmod +x): jalankan `php -S localhost:8000 -t public public/index.php` lalu `open http://localhost:8000`
  - `start.bat` (Windows): perintah yang sama lalu `start http://localhost:8000`
  - `start.sh` (Linux)

C. SQLite
- File `database/app.sqlite`. Jika belum ada, aplikasi otomatis membuat file, menjalankan migrasi, dan mengisi data contoh saat pertama dibuka.
- Sediakan start script yang sama seperti mode localStorage.

Untuk MySQL dan SQLite: satu kelas `Database` yang mendukung driver mysql dan sqlite; pindah driver cukup mengganti `DB_DRIVER` di `.env`. Perbedaan SQL antar-driver (AUTO_INCREMENT vs AUTOINCREMENT, ENUM, dll.) ditangani di migrasi per driver dengan komentar penjelasan.

### Server & clean URL
- Document root = `public/`. Hanya folder ini yang bisa diakses browser.
- Valet: http(s)://{slug}.test (Tahap 2).
- Tanpa Valet: `php -S localhost:8000 -t public public/index.php`. `index.php` harus `return false` untuk file statis yang ada agar CSS/JS tersaji.
- Sertakan `public/.htaccess` (Apache/XAMPP/Laragon) yang meneruskan request non-file ke index.php.
- Clean URL: tanpa ".php" dan tanpa "?page=". Contoh: `/`, `/login`, `/produk`, `/produk/tambah`, `/produk/12`, `/produk/12/edit`.
- Router buatan sendiri: GET & POST, parameter `{id}`, method spoofing `_method=PUT/DELETE`, halaman 404 & 405 yang rapi, normalisasi trailing slash.
- Setelah POST berhasil: redirect (Post/Redirect/Get) + flash message.

### Struktur folder (modular)
```
{slug}/
|-- public/                    # document root
|   |-- index.php              # front controller: satu pintu masuk semua request
|   |-- .htaccess
|   `-- assets/
|       |-- css/  tokens.css, base.css, components.css, pages/*.css
|       |-- js/   app.js, store.js (mode localStorage), modules/*.js
|       `-- img/
|-- app/
|   |-- Core/                  # App, Router, Request, Response, Database, View,
|   |                          # Session, Csrf, Validator, Auth, ErrorHandler
|   |-- Modules/
|   |   `-- {NamaModul}/       # satu folder = satu fitur
|   |       |-- routes.php
|   |       |-- {NamaModul}Controller.php
|   |       |-- {NamaModul}Repository.php   # semua query SQL modul ini
|   |       `-- views/  index.php, form.php, show.php
|   |-- Views/
|   |   |-- layouts/   app.php, auth.php
|   |   |-- partials/  navbar.php, sidebar.php, flash.php, pagination.php
|   |   `-- errors/    404.php, 405.php, 500.php
|   `-- helpers.php            # e(), url(), asset(), old(), redirect(), dd()
|-- config/      app.php, database.php
|-- database/    migrations/, seeders/, app.sqlite (mode SQLite)
|-- storage/     logs/, cache/, uploads/
|-- bin/         migrate.php, seed.php
|-- start.command / start.bat / start.sh   (mode localStorage & SQLite)
|-- .env.example
`-- README.md
```
Aturan modul:
- Satu modul = satu folder di `app/Modules`, mendaftarkan route sendiri lewat `routes.php` (dimuat otomatis oleh App).
- Controller tipis: terima request -> validasi -> panggil Repository -> render view / redirect.
- SQL hanya di Repository. View hanya menampilkan data, tanpa query dan logika bisnis.
- Menambah modul baru tidak boleh mengubah file di `app/Core`.

### Keamanan minimum
- Prepared statement untuk semua query; tidak ada SQL hasil gabungan string input.
- Escape semua output dengan `e()` (htmlspecialchars, ENT_QUOTES, UTF-8).
- Token CSRF di semua form POST/PUT/DELETE, divalidasi di server.
- `password_hash()` / `password_verify()`; `session_regenerate_id(true)` setelah login.
- Cookie session: HttpOnly, SameSite=Lax, Secure saat HTTPS (valet secure).
- Validasi input di server (wajib) dan di browser (pelengkap).
- Upload (jika ada): cek MIME dengan finfo, batasi ukuran, ganti nama file, simpan di `storage/uploads`.
- `APP_DEBUG=true` di lokal menampilkan detail error; `APP_DEBUG=false` menampilkan halaman 500 ramah dan mencatat detail ke `storage/logs/app.log`.

### Standar kode (mudah dibaca junior developer)
- Gaya PSR-12. Nama variabel/fungsi/kelas bahasa Inggris yang deskriptif; komentar bahasa Indonesia.
- Setiap file diawali docblock singkat: fungsi file, dipanggil oleh siapa, memanggil apa.
- Komentar menjelaskan ALASAN, bukan mengulang kode. Tandai langkah penting, mis. `// Langkah 1: validasi input`.
- `public/index.php` memuat komentar "ALUR REQUEST" dari URL masuk sampai HTML keluar.
- Fungsi kecil dan fokus (idealnya < 30 baris); file idealnya < 300 baris.
- Tanpa abstraksi berlebihan: tanpa DI container, tanpa magic method membingungkan, tanpa pattern yang tidak dibutuhkan.
- Type hint untuk parameter dan return. Fitur modern PHP (match, enum, readonly, named arguments, nullsafe) dipakai bila membuat kode lebih jelas.
- Semua modul memakai pola yang sama, sehingga junior cukup mempelajari satu modul untuk memahami semuanya.

### UI & UX yang informatif
- Teks tombol dan label jelas ("Simpan produk", bukan "Submit").
- Setiap aksi memberi umpan balik: toast/flash sukses & gagal, error per field di bawah input, nilai input lama tetap terisi saat validasi gagal.
- Empty state (pesan + tombol aksi), loading state pada tombol saat submit, konfirmasi hapus dengan modal buatan sendiri (bukan alert/confirm).
- Tabel data: pencarian, pagination, sorting bila datanya banyak.
- Responsif mobile-first (breakpoint sekitar 640px dan 1024px).
- Aksesibel: label di setiap input, fokus keyboard terlihat, kontras memadai, alt gambar.
- Data contoh realistis dalam bahasa Indonesia, bukan lorem ipsum.

### Animasi yang smooth
- Animasikan hanya `transform` dan `opacity` agar tetap 60fps. Jangan animasikan width, height, top, left, margin.
- Durasi 150-250ms untuk micro-interaction, 250-400ms untuk modal, drawer, toast. Easing default `cubic-bezier(0.22, 1, 0.36, 1)`. Simpan durasi & easing sebagai CSS variable di tokens.css.
- Utamakan CSS transition/keyframes; JS (Web Animations API / requestAnimationFrame) hanya bila perlu kontrol.
- Reveal on scroll dengan IntersectionObserver, bukan event scroll.
- View Transitions API boleh sebagai progressive enhancement.
- Hormati `prefers-reduced-motion`.
- Hindari layout shift: tetapkan ukuran gambar/elemen; `will-change` seperlunya.

### Urutan kerja
1. Core (router, database, view, session, CSRF, error handler) + layout dasar.
2. Modul satu per satu; uji setiap modul sebelum lanjut ke berikutnya.
3. Poles UI, animasi, responsif, empty/loading/error state.

---

## Tahap 6 - Referensi UI: Sassly

Default referensi (kecuali user memberi referensi lain di Q4):
- URL: https://uiux.sassly.ai/html/index.html
- Basic auth: dibaca dari variabel lingkungan `SASSLY_USER` dan `SASSLY_PASSWORD` (minta ke pemilik skill).
  Jangan menuliskan kredensial ini di chat, file proyek, atau commit. Jika variabel belum diset, tanyakan ke user.

Cara akses:
- Terminal:
  ```bash
  mkdir -p /tmp/sassly-ref
  curl -su "$SASSLY_USER:$SASSLY_PASSWORD" https://uiux.sassly.ai/html/index.html -o /tmp/sassly-ref/index.html
  ```
  Lalu ambil file CSS yang di-link (cari `href="...css"`) dan halaman lain yang di-link dari menu navigasinya dengan cara yang sama. Simpan di /tmp, BUKAN di folder proyek.
- Browser: buka https://uiux.sassly.ai/html/index.html lalu isi dialog login dengan kredensial `SASSLY_USER` / `SASSLY_PASSWORD`.

Langkah:
1. Jelajahi index dan halaman lain dari navigasinya. Catat komponen yang tersedia (layout dashboard, sidebar, navbar, kartu statistik, tabel, form, tombol, badge, modal, alert/toast, tabs, pagination, chart, empty state, dll.).
2. Ekstrak design tokens: warna (hex), font, skala ukuran teks, spacing, radius, shadow, durasi transisi -> `public/assets/css/tokens.css`.
3. Buat peta komponen: setiap kebutuhan aplikasi -> komponen Sassly yang dipakai, atau "dibuat sendiri".
4. Jika tidak ada komponen yang pas, BUAT SENDIRI komponennya memakai token yang sama, sehingga tampil satu keluarga dengan template.
5. Tulis ulang markup & CSS secara native dan rapi. Jangan copy-paste mentah file template, dan jangan ikut memasang library vendor yang dipakai template (mis. Bootstrap/jQuery); implementasikan ulang dengan CSS/JS native.
6. Jangan menyalin logo atau nama brand template. Ikon memakai SVG inline sederhana.
7. Jika situs tidak bisa diakses (401, timeout, diblokir), beri tahu user dan minta screenshot. Jangan menebak tampilannya.

---

## Tahap 7 - Definisi selesai (pastikan tidak ada error)

Pekerjaan BELUM selesai sebelum semua poin lolos dan hasilnya dilaporkan:
- [ ] Lint semua PHP: `find . -name "*.php" -not -path "./storage/*" -print0 | xargs -0 -n1 php -l`
- [ ] Dijalankan dengan `error_reporting(E_ALL)`: tidak ada error, warning, notice, deprecated.
- [ ] MySQL: migrasi + seeder dari database kosong berhasil. SQLite: hapus app.sqlite, buka aplikasi, file dibuat ulang otomatis. localStorage: kosongkan storage, data contoh muncul lagi.
- [ ] Setiap route diuji dengan `curl -sk -o /dev/null -w "%{http_code}" <url>`: tidak ada 500; URL tak dikenal -> 404.
- [ ] Setiap form diuji: data valid tersimpan, data tidak valid menampilkan error, token CSRF salah ditolak.
- [ ] `storage/logs/app.log` bersih setelah pengujian.
- [ ] Console browser tanpa error JS dan tanpa aset 404 (cek dengan browser jika tersedia; jika tidak, tulis bahwa poin ini belum dicek).
- [ ] Tampilan dicek di lebar 375px, 768px, 1280px.
- [ ] Site terbuka di http(s)://{slug}.test via Valet.
- [ ] Mode localStorage/SQLite: salinan proyek di folder lain jalan dari nol hanya dengan start script.

Poin yang tidak bisa diuji ditulis jujur di laporan beserta alasannya.

---

## Tahap 8 - Serah terima

A. README.md (bahasa Indonesia):
- Deskripsi dan daftar fitur
- Kebutuhan sistem (versi PHP & ekstensi hasil Tahap 3)
- Setup dengan Valet: folder, `valet link`, `valet secure`, URL
- Menjalankan di PC lain: start script / `php -S` (localStorage & SQLite), atau setup MySQL + `.env` + migrate (MySQL)
- Akun login demo
- Struktur folder + penjelasan setiap folder
- Diagram alur request (ASCII)
- Tutorial "Cara menambah modul baru" langkah demi langkah
- Daftar route

B. Laporan akhir di chat: URL aplikasi, akun login, hasil checklist Tahap 7, keterbatasan yang diketahui, saran langkah berikutnya.

C. Khusus localStorage/SQLite, beri tahu cara membagikan ke orang lain: zip folder proyek tanpa `storage/logs`, penerima cukup punya PHP 8+ (XAMPP, Laragon, atau Homebrew PHP), lalu double-click start script.

---

## Larangan
- Jangan meninggalkan TODO, placeholder, tombol, atau link yang tidak berfungsi.
- Jangan memakai fungsi usang/berbahaya (mysql_*, eval, extract() pada input).
- Jangan menaruh kredensial atau file sensitif di dalam `public/`.
- Jangan mengubah konfigurasi global laptop (php.ini, MySQL, Valet park) tanpa bertanya dulu.
- Jangan menghapus atau menimpa file di luar folder proyek.

---

## Prompt lanjutan (setelah aplikasi jadi)
- Tambah modul: baca README + satu modul yang ada, ikuti polanya persis, migrasi baru (jangan ubah migrasi lama), tambah menu, jalankan Tahap 7 untuk modul itu, perbarui README.
- Perbaiki bug: reproduksi dulu, jelaskan penyebab utama dengan bahasa junior, perbaiki di akar masalah dengan perubahan minimal, uji ulang, laporkan file yang diubah.
- Naik dari localStorage/SQLite ke MySQL: ganti DB_DRIVER, sesuaikan migrasi, buat skrip pindah data sekali jalan, jalankan ulang Tahap 7.