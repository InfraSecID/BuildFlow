# BuildFlow

> Kumpulan aturan Markdown modular untuk membantu AI coding agent mengikuti praktik engineering lintas bahasa, platform, arsitektur, DevOps, dan keamanan.

[![CI](https://img.shields.io/badge/CI-belum_dikonfigurasi-lightgrey)]()
[![Coverage](https://img.shields.io/badge/coverage-N%2FA-lightgrey)]()
[![License](https://img.shields.io/badge/license-belum_ditetapkan-lightgrey)]()
[![Version](https://img.shields.io/badge/version-belum_ditetapkan-lightgrey)]()

---

## 📋 Daftar Isi

- [Tentang Project](#-tentang-project)
- [Fitur Utama](#-fitur-utama)
- [Tech Stack](#-tech-stack)
- [Arsitektur](#-arsitektur)
- [Prasyarat](#-prasyarat)
- [Instalasi](#-instalasi)
- [Konfigurasi](#-konfigurasi)
- [Cara Menjalankan](#-cara-menjalankan)
- [Testing](#-testing)
- [Build & Deploy](#-build--deploy)
- [Struktur Folder](#-struktur-folder)
- [API Documentation](#-api-documentation)
- [Keamanan](#-keamanan)
- [Kontribusi](#-kontribusi)
- [Lisensi](#-lisensi)
- [Kontak](#-kontak)

---

## 📖 Tentang Project

BuildFlow adalah kumpulan panduan dan checklist berbentuk Markdown di `.agents/rules/`. Isinya ditujukan untuk membantu AI coding agent dan developer menerapkan praktik yang konsisten saat merancang, menulis, menguji, dan meninjau software.

Aturannya mencakup workflow engineering umum, berbagai bahasa dan framework, platform web dan mobile, arsitektur, DevOps, serta keamanan aplikasi dan supply chain. Ini adalah dokumentasi/prompt rules, bukan aplikasi yang dapat dijalankan: repository ini tidak berisi kode aplikasi, dependency manifest, layanan, atau mekanisme untuk memuat rules secara otomatis.

Target pengguna adalah developer dan tim yang ingin memberikan pedoman teknis sebagai konteks bagi AI coding agent. Dukungan pemuatan otomatis bergantung pada tool yang digunakan; verifikasi konfigurasi tool tersebut secara terpisah.

## ✨ Fitur Utama

- ✅ **Aturan workflow engineering** — mencakup error handling, dependency, konfigurasi, testing, Git, dokumentasi, dan Definition of Done (`01-common.md`).
- ✅ **Panduan multi-bahasa dan platform** — dari TypeScript, Python, Go, Rust, Kotlin, Swift hingga web, Android, iOS, desktop, game, dan IoT (`02-languages.md`, `03-platforms.md`).
- ✅ **Panduan framework** — meliputi framework frontend, mobile, dan backend yang disebut di `04-frameworks.md`.
- ✅ **Panduan arsitektur dan DevOps** — membahas pola arsitektur, sistem terdistribusi, CI/CD, container, cloud, observability, dan reliability (`05-devops.md`, `07-architecture.md`).
- ✅ **Panduan keamanan dan checklist** — berisi praktik keamanan, threat modeling, security testing, dan checklist lintas role (`06-security.md`, `08-checklist.md`).
- ✅ **Panduan dokumentasi dan penggunaan agent** — aturan README serta identitas/protokol agent (`09-readme-prompt.md`, `10-agents-md.md`).
- 🚧 **Integrasi dan validasi otomatis** — belum ditemukan konfigurasi loader, test, lint, atau pipeline di repository ini.
- 📅 **Roadmap fitur** — belum didefinisikan.

## 🛠️ Tech Stack

| Layer | Teknologi / Status |
|-------|--------------------|
| Format konten | Markdown |
| Susunan rules | File Markdown di `.agents/rules/`, beberapa memakai frontmatter `trigger` |
| Runtime aplikasi | Tidak ada |
| Frontend / Backend | Tidak ada |
| Database / Cache / Queue | Tidak ada |
| DevOps / Cloud / CI/CD | Belum dikonfigurasi di repository ini |
| Monitoring | Tidak ada konfigurasi monitoring di repository ini |

Nama tool, framework, database, dan layanan cloud yang muncul di dokumen rules adalah contoh/pedoman untuk proyek yang mengadopsinya; itu bukan dependency atau layanan yang digunakan oleh BuildFlow.

## 🏗️ Arsitektur

BuildFlow adalah repository dokumentasi berbasis file, bukan monolith aplikasi, microservice, atau sistem serverless. Alur konseptual penggunaannya:

```text
┌─────────────────────┐     membaca / memberi konteks     ┌─────────────────────┐
│ Developer atau tim  │ ─────────────────────────────────▶│ AI coding agent     │
└─────────────────────┘                                   └──────────┬──────────┘
                                                                     │
                                                         memuat sesuai dukungan tool
                                                                     ▼
                                                         ┌─────────────────────┐
                                                         │ .agents/rules/*.md  │
                                                         └─────────────────────┘
```

Diagram ini menggambarkan pemakaian dokumentasi, bukan integrasi otomatis yang dikonfigurasi oleh repository. Tidak ada berkas ADR di tree saat ini.

## 📦 Prasyarat

- Git hanya diperlukan jika rules diambil atau dibagikan melalui repository Git.
- Editor teks/Markdown untuk membaca atau menyalin rules.
- AI coding agent bersifat opsional; pemuatan otomatis bergantung pada dukungan dan konfigurasi agent tersebut.

Tidak ada versi runtime, database, Docker, atau tool build yang disyaratkan oleh repository ini.

## 🚀 Instalasi

Repository ini tidak mempunyai package manager atau proses instalasi dependency. Untuk menggunakan rules dalam proyek lain, salin direktori `.agents/rules/` ke root proyek tujuan:

```bash
cp -R /path/to/BuildFlow/.agents /path/to/proyek-tujuan/
```

Ganti kedua path contoh di atas dengan path lokal yang benar. Jika menggunakan salinan lokal repository ini sebagai workspace, tidak diperlukan instalasi tambahan.

## ⚙️ Konfigurasi

Tidak ada konfigurasi environment yang didefinisikan dalam repository saat ini:

| Nama | Wajib | Keterangan |
|------|-------|------------|
| Environment variable | — | Tidak ditemukan |
| `.env.example` | — | Tidak tersedia; jangan mengasumsikan ada konfigurasi aplikasi |
| Konfigurasi loader/agent | — | Tidak tersedia; ikuti dokumentasi tool AI yang dipakai |

Frontmatter `trigger` yang ada pada sebagian rules adalah metadata di dalam dokumen; dukungan dan perilakunya bergantung pada tool yang membaca file tersebut.

## ▶️ Cara Menjalankan

BuildFlow tidak memiliki server atau executable. Buka file rules yang relevan dan berikan sebagai konteks kepada AI coding agent, atau gunakan mekanisme pemuatan rules yang didukung oleh tool pilihan Anda.

Untuk melihat daftar rules:

```bash
ls -la .agents/rules/
```

## 🧪 Testing

Belum ada test suite, test runner, atau script pengujian yang ditemukan. Tidak ada perintah testing otomatis yang dapat dijalankan dari repository ini.

## 📦 Build & Deploy

Tidak ada proses build, Dockerfile, konfigurasi deployment, atau CI/CD yang ditemukan. Repository ini digunakan sebagai dokumen; perubahan dapat dibagikan melalui mekanisme version control atau distribusi yang disediakan maintainer.

## 🗂️ Struktur Folder

Struktur berikut sesuai dengan file yang tersedia:

```text
BuildFlow/
├── .agents/
│   └── rules/
│       ├── 01-common.md
│       ├── 02-languages.md
│       ├── 03-platforms.md
│       ├── 04-frameworks.md
│       ├── 05-devops.md
│       ├── 06-security.md
│       ├── 07-architecture.md
│       ├── 08-checklist.md
│       ├── 09-readme-prompt.md
│       └── 10-agents-md.md
└── README.md
```

## 📚 API Documentation

Tidak ada API, route, atau spesifikasi OpenAPI/Swagger di repository ini.

## 🔒 Keamanan

`06-security.md` dan bagian terkait di rules lain berisi panduan keamanan untuk proyek yang mengadopsinya, termasuk validasi input, pengelolaan secret, prinsip least privilege, auth/session, threat modeling, dan security testing. Panduan tersebut merupakan materi dokumentasi; keberadaannya tidak berarti BuildFlow menjalankan kontrol keamanan atau menjamin proyek pengguna aman.

Checklist saat menerapkan rules:

- Jangan menaruh secret atau data sensitif dalam file rules maupun contoh yang dibagikan.
- Tinjau relevansi dan akurasi rekomendasi terhadap konteks, stack, dan threat model proyek.
- Verifikasi kontrol keamanan pada implementasi dan pipeline proyek yang mengadopsi rules.
- Hindari menganggap contoh teknologi di dokumen sebagai konfigurasi yang sudah aktif.

## 🤝 Kontribusi

Kontribusi dapat dilakukan dengan memperbarui dokumen rules atau README berdasarkan praktik yang dapat diverifikasi. Repository ini belum menyediakan `CONTRIBUTING.md` atau instruksi Pull Request khusus.

Sebelum mengirim perubahan:

1. Pastikan saran sesuai konteks dan tidak menyiratkan kontrol/fitur yang belum ada.
2. Periksa konsistensi istilah, struktur, dan tautan antar-dokumen.
3. Jika repository Git hosting tersedia, ajukan perubahan melalui mekanisme review yang berlaku di sana.

## 📄 Lisensi

Lisensi repository belum dinyatakan dan file lisensi tidak ditemukan. Status lisensi: **[perlu dilengkapi]**. Pastikan lisensi ditetapkan oleh pemilik proyek sebelum mendistribusikan atau menggunakan ulang konten.

## 📬 Kontak

Kontak maintainer atau kanal dukungan belum dicantumkan: **[perlu dilengkapi]**.
