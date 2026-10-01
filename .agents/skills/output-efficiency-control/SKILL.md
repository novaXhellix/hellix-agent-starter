---
name: output-efficiency-control
description: >-
  Kontrol efisiensi output (Token Saver), Conciseness Control, dan Token Budgeting.
  Gunakan skill ini untuk membatasi verbose output, mengontrol batas pengeluaran token, dan menerapkan early stopping saat poin utama tercapai.
---

# Output Efficiency Control (Token Saver)

Skill ini dirancang untuk memaksimalkan efisiensi token output yang dihasilkan oleh model.

---

## 1. Conciseness Control
- **To-The-Point Responses**: Menghilangkan basa-basi, filler sentences, dan redundansi intro/outro yang tidak bernilai teknis.
- **Direct Code / Action**: Berikan jawaban langsung berupa diff kode, solusi terarah, atau langkah instruksi terstruktur.
- **Tabel & Bullet Format**: Gunakan format ringkas (bullet points, tabel singkat) jika lebih hemat token dibanding paragraf narasi panjang.

---

## 2. Early Stopping & Token Budgeting
- **Token Budget Allocation**: Tetapkan estimasi batas maksimal token output (`max_tokens`) per operasi berdasarkan kompleksitas tugas.
- **Goal-Oriented Completion**: Hentikan generasi teks begitu tujuan utama atau kode inti telah selesai dipaparkan.
- **Incremental Delivery**: Untuk keluaran yang sangat panjang, pecah menjadi pengiriman bertahap (chunking) hanya jika diminta eksplisit oleh pengguna.
