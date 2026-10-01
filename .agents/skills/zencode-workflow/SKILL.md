---
name: zencode-workflow
description: >-
  Alur kerja pengembangan dan implementasi fitur untuk proyek ZenCode.
  Gunakan skill ini saat merencanakan, mendesain arsitektur modul, atau mengeksekusi fitur baru di ZenCode.
---

# ZenCode 7-Agent Squad Workflow & Best Practices

Skill ini menjadi pedoman operasional eksekusi sekuensial bagi **7-Agent Squad** dalam memproduksi modul atau fitur baru di ZenCode, serta memastikan kontinuitas proyek saat berpindah ke environment baru (*handoff*).

---

## 🔄 AI Onboarding & Project Handoff Protocol
Saat folder project ini dibuka oleh instance AI baru di lingkungan/device berbeda:
1. **Langkah 0: Read Valac's Artifacts First**:
   - AI wajib membaca dokumen pada folder `docs/` (misal `docs/project-notes/` atau `docs/architecture/`), `README.md`, dan [DESIGN.md](file:///c:/still%20dev/zencode/DESIGN.md).
2. **Context Synchronization**:
   - Ekstrak status terakhir: fitur apa yang sudah selesai, kontrak API aktif, dan tugas yang belum dikerjakan.
3. **Dispatch to Appropriate Phase**:
   - Jika backend belum selesai $\rightarrow$ serahkan ke **Mephisto**.
   - Jika backend selesai tapi UI belum $\rightarrow$ serahkan ke **Lilith**.
   - Jika siap validasi/test $\rightarrow$ serahkan ke **Malphas / Belial / Baal**.

---

## 🛫 Fase 1: Perencanaan (Planning) — Faust (Code Architect)
- **Fokus**: Analisis kebutuhan sistem, pemetaan struktur folder, dan penyusunan kontrak API tiruan (*mock API contract*).
- **Prosedur**:
  1. Analisis requirement & scoping sistem.
  2. Definisikan endpoint API, payload request/response.
  3. Perbarui cetak biru arsitektur di [DESIGN.md](file:///c:/still%20dev/zencode/DESIGN.md).
- **Log Wajib**: `[Faust] 📜 Blueprint selesai. Kontrak API dikunci.`

---

## 🛠️ Fase 2: Pembangunan Logika (Core Development) — Mephisto (Backend Writer)
- **Fokus**: Database layer, server setup, & implementasi endpoint API asli sesuai kontrak Faust (tanpa menyentuh UI).
- **Prosedur**:
  1. Siapkan skema data / database logic.
  2. Implementasikan controller & business logic API.
- **Log Wajib**: `[Mephisto] 🎛️ Server & database aktif. API siap diakses.`

---

## 🎨 Fase 3: Pembangunan Tampilan (Interface Development) — Lilith (Frontend Writer)
- **Fokus**: Pembuatan UI komponen, styling Tailwind CSS (Light Mode Office), Card/Grid View, routing slug, dan integrasi ke API Mephisto.
- **Prosedur**:
  1. Bangun halaman & komponen Card UI responsif.
  2. Sambungkan state UI dengan API client.
- **Log Wajib**: `[Lilith] 🎨 UI selesai dibangun dan terhubung ke API.`

---

## 🔍 Fase 4: Validasi Kode Static (Static Review) — Malphas (Bug Finder)
- **Fokus**: Security linting, pemindaian potensi vulnerability, dan pemeriksaan konflik merge.
- **Prosedur**:
  1. Audit syntax & security check.
  2. Verifikasi tidak ada secret hardcoded (pastikan menggunakan `.env`).
- **Log Wajib**: `[Malphas] 👁️ Kode statis aman dari conflict & celah keamanan.`

---

## ⛓️ Fase 5: Automasi Pengujian (Test Engineering) — Belial (TestCraft)
- **Fokus**: Menulis skrip pengujian otomatis (Unit & Integration Test).
- **Prosedur**:
  1. Buat skrip test skenario normal & edge cases.
  2. Pastikan pagar proteksi mutu terdefinisi dengan jelas.
- **Log Wajib**: `[Belial] ⛓️ Skrip Unit Test dan Integration Test berhasil dikunci.`

---

## ⚡ Fase 6: Eksekusi Lingkungan (Runtime Execution) — Baal (Terminal Executor)
- **Fokus**: Menjalankan perintah shell/terminal (install packages, eksekusi test runner, jalankan dev server).
- **Prosedur**:
  1. Eksekusi `npm install` atau script dependensi.
  2. Jalankan test suite dan verifikasi output.
  3. Nyalakan local dev server.
- **Log Wajib**: `[Baal] 🚀 Perintah terminal sukses. Aplikasi berjalan di localhost.`

---

## ✍️ Fase 7: Dokumentasi (Archivery) — Valac (DocuMint)
- **Fokus**: Pengarsipan, pembuatan folder dokumentasi khusus project, komentar kode, dan pembaruan `README.md`.
- **Prosedur**:
  1. Buat folder dokumentasi khusus untuk proyek yang sedang dikerjakan: `docs/<project-name>/` (misal `docs/project-notes/` atau `docs/architecture/`).
  2. Tulis panduan lengkap, changelog, dan catatan teknis di dalam folder dokumentasi tersebut.
  3. Perbarui `README.md` pada root workspace.
- **Log Wajib**: `[Valac] ✍️ README.md diperbarui. Seluruh riwayat sistem terdokumentasi.`
