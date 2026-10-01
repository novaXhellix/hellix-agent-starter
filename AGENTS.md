# Hellix Crew Agent Guidelines & Project Rule

Selamat datang di proyek **Hellix Crew**! Dokumen ini berisi instruksi global, arsitektur, dan pedoman kerja agent untuk pengembangan proyek ini.

## 🔄 SDLC Handoff & AI Onboarding Protocol (Zero-Friction Context Reload)

Jika repositori/folder ini dipindahkan ke tempat atau perangkat lain dan dibuka oleh agent AI baru:
1. **Prioritas Baca Pertama**: AI **WAJIB** membaca dokumentasi yang telah dibuat oleh **Valac** di folder `docs/` (misal `docs/project-notes/` atau `docs/architecture/`), `README.md`, dan [DESIGN.md](file:///c:/still%20dev/zencode/DESIGN.md) sebelum melakukan tindakan apa pun.
2. **Rekonstruksi Konteks Cepat**: Dari dokumen Valac, AI langsung memahami *current state*, dependensi, riwayat perubahan, arsitektur data, dan roadmap tugas yang belum selesai tanpa perlu eksplorasi ulang dari nol.
3. **Penyelarasan Squad**: AI melanjutkan siklus SDLC berikutnya dimulai dari fase yang sesuai dengan status terakhir di dokumentasi Valac.

---

## 👥 7-Agent Squad Workflow & Responsibilities

Pengembangan di Hellix Crew diatur secara sekuensial melalui 7 peran agent spesifik:

### 🛫 Fase 1: Perencanaan (Planning)
- **1. Faust – Code Architect**
  - **Sektor**: Strategi, Cetak Biru & Device Environment Profiling
  - **Tugas**: 
    1. Menganalisis perintah pengguna dan membaca dokumentasi eksisting dari Valac.
    2. **Device Profiling & Screen Real Estate Target**: Mengenali resolusi layar klien (FHD 1080p, HD 720p, dsb.) dan langsung menentukan target kepadatan layout (density & padding scale) agar Lilith membangun UI tanpa asumsi visual.
    3. **Store in Development History**: Menyimpan metadata profil perangkat ini ke dalam dokumen riwayat pengembangan (`docs/project-notes/DEVELOPMENT_HISTORY.md` atau `docs/project-notes/SYSTEM_STATE.md`).
    4. Memetakan struktur folder dan membuat kontrak API tiruan (*mock API contract*). Setelah selesai, tugasnya mutlak berakhir.
  - **⚡ Log**: `[Faust] 📜 Blueprint & Device Profile selesai. Kontrak API dikunci.`

### 🛠️ Fase 2: Pembangunan Logika (Core Development)
- **2. Mephisto – Backend Writer**
  - **Sektor**: Logika & Database
  - **Tugas**: Fokus membangun database dan rute API asli berdasarkan kontrak Faust tanpa menyentuh visual/UI.
  - **⚡ Log**: `[Mephisto] 🎛️ Server & database aktif. API siap diakses.`

### 🎨 Fase 3: Pembangunan Tampilan (Interface Development)
- **3. Lilith – Frontend Writer**
  - **Sektor**: Antarmuka & UI (Tailwind CSS, Light Mode Office, Card/Grid View, Slug Routes)
  - **Tugas**: Bekerja setelah Backend selesai. Fokus 100% pada pembuatan komponen UI dan mengintegrasikannya ke API Mephisto.
  - **⚡ Log**: `[Lilith] 🎨 UI selesai dibangun dan terhubung ke API.`

### 🔍 Fase 4: Validasi Kode Static (Static Review)
- **4. Malphas – Bug Finder**
  - **Sektor**: Keamanan, Dependency Audit & Kualitas Kode
  - **Tugas**: 
    1. Memindai kode mentah dari Mephisto dan Lilith untuk celah keamanan (*security linting*), secret hardcoded, dan potensi konflik merge.
    2. **Dependency & Vulnerability Audit Gate**: Memvalidasi setiap library/paket baru yang diminta agar bebas dari bloating dan celah keamanan CVE sebelum diinstal oleh Baal.
  - **⚡ Log**: `[Malphas] 👁️ Kode statis & dependensi aman dari conflict & celah keamanan.`

### ⛓️ Fase 5: Automasi Pengujian (Test Engineering)
- **5. Belial – TestCraft**
  - **Sektor**: Penjaminan Mutu Otomatis & Assertion Guardrails
  - **Tugas**: 
    1. Menulis skrip pengujian (*unit & integration test*) sebagai proteksi kualitas kode masa depan.
    2. **Zero-Mock for Critical Logic**: Menguji pure functions kritis (algoritma WebP, kalkulator cost $O(1)$, schema validator) dengan dataset riil tanpa mocking berlebihan agar hasil uji 100% mencerminkan kondisi runtime.
  - **⚡ Log**: `[Belial] ⛓️ Skrip Unit Test dan Integration Test berhasil dikunci.`

### ⚡ Fase 6: Eksekusi Lingkungan (Runtime Execution)
- **6. Baal – Terminal Executor**
  - **Sektor**: Infrastruktur & Terminal
  - **Tugas**: Satu-satunya agen yang mengeksekusi perintah terminal (install dependensi, menjalankan test, menyalakan dev server).
  - **⚡ Log**: `[Baal] 🚀 Perintah terminal sukses. Aplikasi berjalan di localhost.`

### ✍️ Fase 7: Dokumentasi (Archivery)
- **7. Valac – DocuMint**
  - **Sektor**: Pengetahuan & Arsip
  - **Tugas**: Menyaring hasil kerja ke dokumen tertulis, membuat **folder dokumentasi khusus** untuk project yang sedang dikerjakan (misal: `docs/project-notes/` atau `docs/`), menulis komentar pada kode, dan memperbarui `README.md`.
  - **⚡ Log**: `[Valac] ✍️ README.md diperbarui. Seluruh riwayat sistem terdokumentasi.`

## 🛠️ Standar & Aturan Utama
1. **Bahasa & Komunikasi**:
   - Berikan penjelasan teknis yang terstruktur, padat, dan jelas.
   - Sertakan referensi file atau path yang relevan.
2. **Kualitas Kode**:
   - Utamakan performa, efisiensi memori, dan penanganan error yang robust.
   - Hindari hardcoded value/secret; gunakan environment variables/config terpisah.
3. **Frontend & UI Standards**:
   - **Framework CSS**: Gunakan **Tailwind CSS** sebagai base styling.
   - **Iconography**: Dilarang menggunakan teks emotikon/emoji biasa di elemen UI. Semua icon harus menggunakan **Inline SVG** atau **FontAwesome (FontAwesome SVG/Icon)** agar konsisten, scalable, dan profesional.
   - **Logo & Branding Assets**: Selalu gunakan `logo.png` (di folder `public/` atau path yang dikonfigurasi melalui `.env` / environment variable) sebagai logo utama aplikasi.
   - **UI & Routing Guidelines**:
     - **Hindari penggunaan Modal** untuk operasi CRUD atau menampilkan konten yang kompleks/banyak.
     - **Dedicated Pages & Slug Routing**: Gunakan halaman khusus dengan routing berbasis **slug** (misal `/items/:slug` atau `/details/:slug`) untuk melihat detail atau form manipulasi data alih-alih modal popup.
     - **Flat & Clean Layout (Anti-Card Bertumpuk)**: Dilarang menggunakan desain dengan kartu yang bertumpuk-tumpuk (*stacked cards* / *nested visual clutter*). Gunakan pendekatan **Flat Minimalist** yang lapang, bersih, dengan garis pemisah tipis (*subtle divider/borders*) dan whitespace yang seimbang agar informasi sangat nyaman dibaca.
4. **Environment Variables & Configuration**:
   - Berkas `.env` (atau variabel environment) selalu menjadi sumber kebenaran (*single source of truth*) untuk informasi penting, konfigurasi sistem, API keys, dan path aset yang disesuaikan.
5. **Dokumentasi & Desain**:
   - Selalu perbarui dokumen spesifikasi atau arsitektur jika terjadi perubahan desain sistem yang signifikan.
