AI Employee UMKM - Hackathon Plan & Implementation Guide
1. Konsep dan Nama Produk
●	Nama Produk: OmniStaff AI (UMKM Autonomous Virtual Workforce)
●	Konsep Utama: Platform pekerja virtual berbasis AI multi-agent yang divisualisasikan dalam bentuk 3D Virtual Office interaktif. Setiap agen AI bertindak sebagai karyawan virtual (seperti Customer Service, Inventory Manager, dan Financial Analyst) yang siap membantu operasional harian UMKM secara mandiri, didukung oleh integrasi text-chat, voice command, dan image generation untuk katalog produk.

________________________________________
2. Masalah yang Diselesaikan
●	Keterbatasan Sumber Daya: Banyak UMKM tidak memiliki anggaran untuk merekrut staf khusus administrasi, layanan pelanggan (CS), dan pencatatan keuangan secara bersamaan.
●	Respon Pelanggan Lambat: Seringkali chat dari pembeli potensial di luar jam operasional terabaikan karena pemilik toko kelelahan atau sibuk mengurus produksi.
●	Manajemen Stok dan Laporan Manual: Pencatatan stok barang dan laporan keuangan harian sering kali berantakan atau terlambat direkap, memicu potensi salah hitung rugi-laba.

________________________________________
3. Fitur MVP (Minimum Viable Product)
●	3D Virtual Office Dashboard: Antarmuka visual kantor interaktif berbasis web di mana avatar staf AI bergerak atau merespon sesuai status kerja.
●	AI Customer Service Agent: Chatbot interaktif untuk menjawab pertanyaan seputar produk, stok, dan status pesanan pelanggan secara otomatis 24/7.
●	Modul Manajemen Produk & Stok: Sinkronisasi inventaris otomatis yang bisa diperintah melalui teks atau suara.
●	Modul Laporan Keuangan Kilat: Pembuatan laporan laba-rugi otomatis berdasarkan transaksi harian yang masuk.

________________________________________
4. Fitur AI
●	Meta AI Integration: Menggunakan model AI mutakhir dari Meta via API untuk penalaran agen, pemrosesan bahasa alami (NLP), dan pemahaman konteks bisnis lokal.
●	Voice-Activated Commands: Memungkinkan pemilik UMKM memberikan instruksi operasional (seperti "Cek stok kain batik hari ini") lewat suara (Speech-to-Text & Text-to-Speech).
●	AI Image Generator: Otomatis menghasilkan aset visual atau poster promosi produk baru berdasarkan deskripsi teks sederhana.

________________________________________
5. Arsitektur Sistem (Meta AI + Node.js + Express + MySQL)
┌────────────────────────────────────────────────────────┐

│               Frontend (Browser / Localhost)           │

│  - 3D Virtual Office (Three.js / Canvas)               │

│  - Chat & Voice Interface                              │

└──────────────────────────┬─────────────────────────────┘

                           │ HTTP / WebSocket API

┌──────────────────────────▼─────────────────────────────┐

│               Backend (Node.js + Express)              │

│  - RESTful API Controllers                             │

│  - AI Orchestrator / Agent Execution Engine            │

│  - Voice & Image Processing Handlers                   │

└───────────┬───────────────────────────┬────────────────┘

            │ SQL Queries               │ Meta AI API Calls

┌───────────▼─────────────┐   ┌─────────▼────────────────┐

│   Database (MySQL)      │   │   Meta AI Model Endpoints│

│  - Customers, Products  │   │   - LLM Reasoning        │

│  - Orders, Reports      │   │   - Image/Voice APIs     │

└─────────────────────────┘   └──────────────────────────┘

________________________________________
6. Struktur Folder Lengkap
Struktur direktori proyek yang terorganisir untuk pengembangan lokal:

ai-employee-umkm/

├── frontend/

│   ├── dashboard/       # Tampilan antarmuka kantor virtual 3D

│   ├── ai-chat/         # Komponen interaksi chat & suara

│   └── assets/          # Gambar, model 3D, dan styling CSS

├── backend/

│   ├── controllers/     # Logika bisnis (products, orders, reports)

│   ├── routes/          # Definisi endpoint API Express

│   ├── services/        # Integrasi service eksternal & database

│   ├── middleware/      # Validasi auth dan error handler

│   └── server.js        # Titik masuk utama aplikasi Express

├── ai/

│   ├── agent/           # Logika AI Agent & System Prompts

│   ├── tools/           # Function calling tools (cek stok, buat laporan)

│   ├── prompts/         # Template prompt untuk berbagai peran agen

│   ├── voice/           # Pengolahan audio/voice workflow

│   └── image/           # Handler pembuatan gambar produk

├── modules/

│   ├── customers/       # Logika manajemen data pelanggan

│   ├── products/        # Logika katalog produk & inventaris

│   ├── orders/          # Logika pemesanan dan transaksi

│   ├── employees/       # Logika status virtual employee

│   └── reports/         # Logika rekap laporan keuangan

├── database/

│   ├── migrations/      # Skema pembuatan tabel database

│   └── seeds/           # Data dummy awal untuk testing

├── docs/

│   ├── architecture.md  # Dokumentasi arsitektur sistem

│   ├── workflow.md      # Panduan alur kerja agen AI

│   └── demo.md          # Skenario rekaman demo hackathon

├── .env                 # Konfigurasi environment (DB credentials, API Keys)

├── package.json         # Dependensi proyek Node.js

└── README.md            # Dokumentasi utama proyek

________________________________________
7. Database Schema (MySQL)
Simpan skema ini di database/migrations/schema.sql:

CREATE DATABASE IF NOT EXISTS ai_employee_umkm;

USE ai_employee_umkm;

-- Tabel Produk & Stok

CREATE TABLE products (

    id INT AUTO_INCREMENT PRIMARY KEY,

    name VARCHAR(255) NOT NULL,

    category VARCHAR(100),

    price DECIMAL(10, 2) NOT NULL,

    stock INT NOT NULL DEFAULT 0,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP

);

-- Tabel Pelanggan

CREATE TABLE customers (

    id INT AUTO_INCREMENT PRIMARY KEY,

    name VARCHAR(255) NOT NULL,

    phone VARCHAR(50) UNIQUE,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP

);

-- Tabel Pesanan

CREATE TABLE orders (

    id INT AUTO_INCREMENT PRIMARY KEY,

    customer_id INT,

    total_amount DECIMAL(10, 2) NOT NULL,

    status VARCHAR(50) DEFAULT 'Pending', -- Pending, Completed, Cancelled

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (customer_id) REFERENCES customers(id)

);

-- Tabel Detail Pesanan

CREATE TABLE order_items (

    id INT AUTO_INCREMENT PRIMARY KEY,

    order_id INT,

    product_id INT,

    quantity INT NOT NULL,

    subtotal DECIMAL(10, 2) NOT NULL,

    FOREIGN KEY (order_id) REFERENCES orders(id),

    FOREIGN KEY (product_id) REFERENCES products(id)

);

-- Tabel Aktivitas AI Employee

CREATE TABLE ai_logs (

    id INT AUTO_INCREMENT PRIMARY KEY,

    agent_name VARCHAR(100),

    action_type VARCHAR(100),

    payload TEXT,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP

);

________________________________________
8. AI Agent + Tool/Function Calling
Di direktori ai/tools/index.js, definisikan fungsi yang bisa dipanggil oleh Meta AI Agent:

const db = require('../../backend/services/db');

const aiTools = {

    checkStock: async ({ productName }) => {

        const [rows] = await db.query('SELECT name, stock, price FROM products WHERE name LIKE ?', [`%${productName}%`]);

        return rows.length > 0 ? rows : `Produk ${productName} tidak ditemukan.`;

    },

    

    createOrder: async ({ customerName, productName, quantity }) => {

        // Logika pembuatan pesanan otomatis

        return `Pesanan untuk ${customerName} sebanyak ${quantity} ${productName} berhasil dibuat.`;

    },

    getDailyReport: async () => {

        const [rows] = await db.query('SELECT SUM(total_amount) as total_sales FROM orders WHERE DATE(created_at) = CURDATE()');

        return `Total penjualan hari ini adalah Rp ${rows[0].total_sales || 0}`;

    }

};

module.exports = aiTools;

________________________________________
9. Voice Workflow
1.	Input Suara: Pengguna menekan tombol mikrofon di frontend dashboard.
2.	Speech-to-Text (STT): Audio direkam di browser, dikirim ke backend, dan diterjemahkan menjadi teks perintah.
3.	AI Processing: Meta AI menerima teks, mengidentifikasi niat (intent), lalu mengeksekusi tool yang sesuai (misalnya checkStock).
4.	Text-to-Speech (TTS) / Response: Hasil eksekusi diubah kembali menjadi respons suara atau ditampilkan dalam bentuk teks di gelembung chat avatar 3D.

________________________________________
10. Image Generation
1.	Pemicu: Pengguna meminta agen membuat poster promosi produk ("Buatkan poster diskon baju batik").
2.	Eksekusi: Backend memanggil Meta Model API endpoint khusus pembuatan gambar (image generation).
3.	Hasil: Gambar dikirim kembali ke frontend dan otomatis disimpan ke direktori frontend/dashboard/assets/promotions/ untuk langsung dibagikan ke media sosial UMKM.

________________________________________
11. Dashboard (3D Virtual Kantor)
●	Visualisasi: Menggunakan canvas interaktif ringan (atau pustaka 3D seperti Three.js) untuk menampilkan tata letak kantor mini.
●	Interaktivitas:
●	Meja 1: Staf CS (Menangani chat masuk dari pelanggan).
●	Meja 2: Staf Gudang (Menampilkan grafik stok menipis secara visual).
●	Meja 3: Staf Keuangan (Menampilkan ringkasan kas masuk hari ini).
●	Klik pada salah satu avatar akan membuka panel chat langsung dengan agen spesifik tersebut.

________________________________________
12. Skenario Demo (3–5 Menit)
●	Menit 0:00 - 1:00 (Pembukaan & Masalah): Menjelaskan tantangan UMKM yang kesulitan merekrut staf operasional lengkap dan mengenalkan OmniStaff AI.
●	Menit 1:00 - 2:30 (Live Demo - Voice & Chat): Pemilik toko menggunakan perintah suara untuk menanyakan stok barang dan mencatat pesanan baru secara real-time melalui kantor virtual 3D.
●	Menit 2:30 - 3:45 (Automasi AI): Mendemonstrasikan pembuatan gambar promosi produk baru menggunakan Meta AI dan rekap laporan keuangan otomatis.
●	Minut 3:45 - 4:00 (Penutup): Ringkasan dampak efisiensi bagi UMKM dan potensi skalabilitas global.

________________________________________
13. Roadmap 10 Hari (Hackathon Schedule)
●	Hari 1: Inisialisasi repositori, perancangan database MySQL, dan struktur folder dasar Express.
●	Hari 2: Pembuatan koneksi database dan implementasi REST API CRUD dasar (Produk & Pesanan).
●	Hari 3: Integrasi awal API Meta AI dan penyusunan system prompts untuk agen virtual.
●	Hari 4: Pengembangan Function Calling / Tools backend (Cek stok, buat pesanan, rekap laporan).
●	Hari 5: Membangun kerangka frontend dashboard kantor virtual sederhana.
●	Hari 6: Implementasi fitur interaksi chat teks di antarmuka web.
●	Hari 7: Integrasi Voice Workflow (Audio capture & Speech processing).
●	Hari 8: Integrasi fitur Image Generation untuk kebutuhan promosi produk.
●	Hari 9: Pengujian menyeluruh (End-to-End Testing) di lingkungan Windows Localhost dan perbaikan bug.
●	Hari 10: Perekaman video demo (3–5 menit), penyusunan dokumentasi README.md, dan finalisasi submission.

________________________________________
14. Checklist Submission
	Repositori GitHub publik dengan struktur folder rapi.
	Skema database SQL dan panduan instalasi lokal di README.md.
	Bukti integrasi Meta AI Model API aktif di backend.
	Dokumentasi arsitektur (docs/architecture.md) dan alur kerja (docs/workflow.md).
	Tautan video demo berdurasi 3–5 menit yang memperlihatkan fitur utama.

________________________________________
15. Rancangan Prototype di Windows Localhost
Ikuti langkah ringkas ini untuk menjalankan prototipe di komputer lokal Anda:

1.	Prasyarat Sistem: Pastikan Node.js, npm, dan XAMPP (untuk MySQL lokal) sudah terpasang di Windows.
2.	Inisialisasi Database: Buka phpMyAdmin, buat database baru bernama ai_employee_umkm, lalu impor skema dari bagian nomor 7.
3.	Setup Backend:

cd backend

npm init -y

npm install express mysql2 dotenv cors body-parser

4.	Konfigurasi .env: Buat file .env di root folder:

PORT=3000

DB_HOST=localhost

DB_USER=root

DB_PASS=

DB_NAME=ai_employee_umkm

META_API_KEY=your_meta_ai_api_key_here

5.	Menjalankan Server:

node backend/server.js

6.	Akses Dashboard: Buka browser dan arahkan ke http://localhost:3000 untuk mulai menguji interaksi kantor virtual AI.
