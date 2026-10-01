---
name: context-compaction-pruning
description: >-
  Strategi kompresi dan pemadatan konteks percakapan serta prompt pruning.
  Gunakan skill ini saat riwayat obrolan terlalu panjang, perlu meringkas memori, atau memotong teks input yang tidak relevan demi menghemat token.
---

# Context Compacting & Prompt Pruning

Skill ini menyediakan prosedur dan algoritma untuk memadatkan konteks serta melakukan pruning pada prompt input.

---

## 1. Context Compacting & Compression
- **Identifikasi Informasi Redundan**: Deteksi percakapan berulang, sapaan/basa-basi, atau log debug yang tidak lagi dibutuhkan.
- **Ekstraksi Inti Percakapan**: Ubah riwayat multi-turn panjang menjadi ringkasan poin-poin struktural (Bullet Points / Key-Value State).
- **Preservasi Entitas Kritis**: Pastikan data penting (nama variabel, path file, konfigurasi kunci, keputusan arsitektur) tidak hilang selama kompresi.

### Template Ringkasan Konteks:
```markdown
### [Context Summary]
- Goal: <Tujuan utama percakapan>
- Keputusan Teknis: <Daftar arsitektur/library yang disepakati>
- State Terkini: <Status implementasi terakhir>
- File Terkait: <Daftar path file aktif>
```

---

## 2. Prompt Pruning
- **Pre-filtering Input**: Buang filler text, instruksi ganda, atau konteks file yang tidak relevan dengan query saat ini.
- **Selective AST / Code Truncation**: Hanya sertakan potongan kode/fungsi spesifik yang ditargetkan alih-alih seluruh isi file.
- **Dead Context Elimination**: Hapus konteks pesan yang telah kedaluwarsa atau terselesaikan pada iterasi sebelumnya.
