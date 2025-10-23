## 📦 1. Prasyarat Server

### Sistem & Software

- **OS**: Ubuntu 22.04+ (atau setara)
- **Web Server**: Apache 2.4 dengan modul `mod_rewrite` aktif
- **PHP**: Versi 8.2.x dengan ekstensi:
  - `intl`, `mbstring`, `pdo_mysql`, `openssl`, `json`, `curl`, `gd`, `fileinfo`, `exif`
- **Database**: MySQL 8.x
- **Node.js**: 18+ (untuk build aset admin web)
- **Composer**: 2.x

> 💡 Rekomendasi pengaturan `php.ini`:
>
> ```ini
> date.timezone = Asia/Jakarta
> memory_limit = 256M
> post_max_size = 32M
> upload_max_filesize = 16M
> max_execution_time = 60
> display_errors = Off
> log_errors = On
> error_log = /var/log/php_errors.log
> ```

---

## 🧱 2. Persiapan Database

Masuk ke MySQL dan buat database serta user khusus:

```sql
CREATE DATABASE dbname CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'username'@'%' IDENTIFIED BY 'ganti_password_kuat';
GRANT ALL PRIVILEGES ON hris_attendance.* TO 'username'@'%';
FLUSH PRIVILEGES;
```

> ⚠️ Gunakan password yang kuat untuk user database produksi.

---

## ⚙️ 3. Instalasi Lokal (Development Setup)

### Clone Repository

```bash
cd C:\laragon\www
git clone https://github.com/itged/absensiv2.git absensiv2
cd absensiv2
```

### Install Dependensi

```bash
# Backend
composer install

# Frontend (Admin Web)
npm install
```

> Jika hanya menggunakan API untuk Flutter, langkah `npm install` bisa dilewati.

---

### Konfigurasi Environment

Salin file `.env` contoh:

```bash
cp env .env
```

Lalu ubah konfigurasi sesuai kebutuhan:

```dotenv
app.baseURL = 'url_app'
app.environment = development

database.default.hostname = 127.0.0.1
database.default.database =
database.default.username =
database.default.password =

jwt.secret = 'ubah_dengan_secret_acak_panjang'
jwt.ttl = 86400
app.defaultLocale = 'id'
```

> 🔑 Gunakan secret JWT acak dan panjang untuk keamanan token.

---

### Migrasi & Seeder Database

```bash
php spark migrate
php spark db:seed DevSeed
```

Seeder akan membuat data awal seperti akun admin dan konfigurasi shift.

---

### Jalankan Server Lokal

Gunakan CodeIgniter built-in server:

```bash
php spark serve
```

Akses di browser:  
👉 [http://localhost:8080](http://localhost:8080)

Atau jika menggunakan Laragon (direkomendasikan):

1. Buka Laragon → Menu → _www → Create Virtual Host_
2. Masukkan nama domain: `testing.test`
3. Restart Apache
4. Akses di browser: [http://testing.test](http://testing.test)

---

### Build Frontend (Admin Panel)

Untuk menampilkan dashboard HRD:

```bash
npm run build
```

Output akan muncul di `public/build/`.

---

## 👤 Akun Default

| Role      | Email                | Password    |
| --------- | -------------------- | ----------- |
| Admin HRD | `admin@company.test` | `Admin@123` |

---

## 🧩 Struktur Folder Proyek

```
hris-attendance/
├── app/                 # Source utama CodeIgniter
│   ├── Controllers/     # API & Web controllers
│   ├── Models/          # Model database
│   ├── Views/           # Template / Vue entry point
│   └── Filters/         # Middleware (JWT & Role)
├── public/              # Root publik (DocumentRoot Apache)
├── writable/            # Uploads, logs, cache, session
├── migrations/          # Struktur tabel database
├── vendor/              # Dependensi PHP
├── package.json         # Dependensi frontend
├── composer.json        # Dependensi backend
├── .env                 # Konfigurasi environment
└── README.md            # Panduan ini
```

---

## ☁️ 4. Deployment ke Server Produksi

### Upload Source Code

```bash
cd /var/www
git clone https://github.com/itged/absensiv2.git
cd absensiv2
```

### Install Dependensi Produksi

```bash
composer install --no-dev --optimize-autoloader
npm ci && npm run build
```

### Konfigurasi `.env` Produksi

```dotenv
app.baseURL = 'https://app_url'
app.environment = production
app.forceGlobalSecureRequests = true

database.default.hostname =
database.default.database =
database.default.username =
database.default.password = ganti_password_kuat

jwt.secret = 'secret_produksi_acak_panjang'
jwt.ttl = 86400
```

### Atur Permission Folder

```bash
sudo chown -R www-data:www-data writable
sudo chmod -R 775 writable
```

### Migrasi Database

```bash
php spark migrate --all
```

---

## 🌐 5. Konfigurasi Apache VirtualHost

Buat file konfigurasi baru:

```bash
sudo nano /etc/apache2/sites-available/absensiv2.conf
```

Isi dengan konfigurasi vhost

Aktifkan site dan restart Apache:

```bash
sudo a2enmod rewrite
sudo a2ensite hris-attendance.conf
sudo systemctl reload apache2
```

---

---

---

## 🧠 8. Tips Developer

- Gunakan **VSCode** dengan ekstensi:
  - PHP Intelephense
  - Tailwind CSS IntelliSense
  - Vetur (Vue)
- Gunakan timezone `Asia/Jakarta`
- Jalankan `php spark routes` untuk melihat semua route aktif
- Gunakan `php spark clear-cache` setelah mengubah konfigurasi

---

## 🧑‍💻 Pengembang

| Nama       | Peran |
| ---------- | ----- |
| IT DEV GED |       |

---

📅 **Terakhir diperbarui:** Oktober 2025  
© 2025 GED Lintas Indonesia — All Rights Reserved.
