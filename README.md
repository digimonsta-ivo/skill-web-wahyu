# skill-web-wahyu

Skill untuk **Claude** (Claude Code / claude.ai) yang membawa Anda dari nol sampai aplikasi web
**PHP native monolith** jalan tanpa error di Laravel Valet.

```
wawancara parameter → setup folder & Valet → cek environment → rencana → coding → verifikasi → serah terima
```

## Apa yang dihasilkan

- **PHP native** (tanpa framework, tanpa Composer, tanpa npm), `declare(strict_types=1)`, autoload PSR-4 buatan sendiri.
- **Monolith**: server merender HTML; `fetch()` hanya untuk interaksi kecil.
- **Pilihan penyimpanan data**: MySQL, SQLite, atau localStorage (dengan start script untuk dibagikan).
- **Clean URL** + router buatan sendiri (GET/POST, `{id}`, method spoofing, 404/405, Post/Redirect/Get).
- **Struktur modular**: satu modul = satu folder (`routes.php`, Controller, Repository, views).
- **Keamanan minimum**: prepared statement, escape output, CSRF, `password_hash`, cookie HttpOnly/SameSite/Secure, validasi server.
- **UI & UX**: referensi design token Sassly, CSS & JS native, animasi `transform`/`opacity`, responsif, aksesibel.
- **Definisi selesai** yang ketat: lint, migrasi/seed dari kosong, uji setiap route & form, log bersih, cek 375/768/1280 px.
- **Serah terima**: README bahasa Indonesia (alur request, cara menambah modul, daftar route).

Bahasa komunikasi & komentar kode: **Indonesia**. Nama variabel/fungsi/kelas: **Inggris**.

## Instalasi

### Claude Code

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/digimonsta-ivo/skill-web-wahyu.git ~/.claude/skills/skill-web-wahyu
```

Lalu di Claude Code, panggil dengan `/skill-web-wahyu` diikuti deskripsi aplikasi, misalnya:

```
/skill-web-wahyu web order untuk pemesanan makanan di kantin kantor
```

### claude.ai (Skills)

Unduh repo ini sebagai ZIP, lalu unggah folder yang berisi `SKILL.md` di **Settings → Capabilities → Skills**.

## Kebutuhan

- macOS dengan [Laravel Valet](https://laravel.com/docs/valet) (opsional; bisa juga `php -S`)
- PHP 8.2+ dengan ekstensi `pdo`, `pdo_mysql`, `pdo_sqlite`, `mbstring`, `json`, `fileinfo`, `openssl`
- MySQL / MariaDB (jika memilih mode MySQL)

## Konfigurasi rahasia (tidak disimpan di repo)

Skill ini **tidak** menyimpan kredensial apa pun. Siapkan sebagai variabel lingkungan, atau berikan saat Claude bertanya:

| Variabel | Dipakai untuk |
|---|---|
| `DB_PASSWORD` | Password MySQL lokal (ditulis ke `.env` proyek yang dibuat) |
| `SASSLY_USER`, `SASSLY_PASSWORD` | Basic auth referensi UI Sassly (`uiux.sassly.ai`). Minta ke pemilik skill. Tanpa ini, Anda bisa memilih "Referensi lain" (kirim link/screenshot) |

Contoh (zsh):

```bash
export DB_PASSWORD='password-mysql-anda'
export SASSLY_USER='...'
export SASSLY_PASSWORD='...'
```

## Struktur proyek yang dihasilkan

```
{slug}/
├── public/        # document root: index.php (front controller), .htaccess, assets/
├── app/
│   ├── Core/      # App, Router, Request, Response, Database, View, Session, Csrf, Validator, Auth, ErrorHandler
│   ├── Modules/   # satu folder per fitur: routes.php, Controller, Repository, views/
│   ├── Views/     # layouts, partials, errors
│   └── helpers.php
├── config/        # app.php, database.php
├── database/      # migrations/, seeders/
├── storage/       # logs/, cache/, uploads/
├── bin/           # migrate.php, seed.php
└── README.md
```

## Prompt lanjutan

Setelah aplikasi jadi, skill juga memandu:

- **Tambah modul** mengikuti pola modul yang ada (migrasi baru, menu, uji, README).
- **Perbaiki bug** dari reproduksi sampai perbaikan di akar masalah.
- **Naik** dari localStorage/SQLite ke MySQL.
