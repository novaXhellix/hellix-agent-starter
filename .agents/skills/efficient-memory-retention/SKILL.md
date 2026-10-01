---
name: efficient-memory-retention
description: >-
  Strategi manajemen retensi memori efisien dengan Sliding Window Memory dan Vector-Based Retrieval (RAG).
  Gunakan skill ini untuk mengelola batas memori aktif dan mencari referensi dokumen/kode secara on-demand tanpa membebani context window.
---

# Efficient Memory Retention Strategy

Skill ini mengatur bagaimana sistem mempertahankan memori jangka pendek dan mengambil memori jangka panjang secara efisien.

---

## 1. Sliding Window Memory
- **Active Windowing**: Hanya mempertahankan $N$ turn percakapan terakhir (atau batas budget $K$ token terakhir) dalam memori aktif model.
- **Archiving Layer**: Pindahkan turn di luar jendela aktif ke penyimpanan terkompresi / snapshot ringkas.
- **Dynamic Context Eviction**: Buang pesan tertua secara FIFO (*First-In, First-Out*) saat batas kapasitas token tercapai.

---

## 2. Vector-Based Memory Retrieval (RAG)
- **On-Demand Injection**: Jangan masukkan seluruh dokumen/basis data ke dalam prompt.
- **Chunking & Indexing**:
  - Potong dokumen menjadi potongan-potongan logis (chunks) dengan metadata (file path, line range).
  - Gunakan embedding vektor untuk pengindeksan semantik.
- **Semantic Query & Top-K Retrieval**:
  - Ambil hanya $Top\text{-}K$ potongan paling relevan dengan query pengguna saat ini.
  - Masukkan chunk hasil retrieval ke dalam konteks prompt hanya ketika dibutuhkan.
