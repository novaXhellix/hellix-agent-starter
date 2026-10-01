---
name: ai-systems-optimization
description: >-
  Teknik arsitektur AI tingkat lanjut termasuk Advanced Tokenization (BPE) dan KV Caching.
  Gunakan skill ini untuk optimasi performa komputasi inferensi, efisiensi encoding token, dan strategi pemanfaatan KV Cache.
---

# AI Systems Architecture & Optimization

Skill ini mencakup panduan teknis pada level arsitektur dan sistem model AI untuk efisiensi komputasi dan tokenisasi.

---

## 1. Advanced Tokenization (Byte-Pair Encoding / Specialized Tokenizer)
- **Token Density Optimization**: Memaksimalkan rasio karakter per token menggunakan kosakata tokenisasi yang optimal (misalnya BPE atau WordPiece yang disesuaikan untuk domain kode pemrograman).
- **Whitespace & Formatting Efficiency**: Mengurangi token overhead yang disebabkan oleh spasi berlebih, indentasi tidak efisien, atau karakter non-standar.
- **Special Token Handling**: Pemanfaatan token kontrol terstruktur untuk memisahkan instruksi vs data konteks secara hemat token.

---

## 2. KV Caching (Key-Value Caching Optimization)
- **Prompt Prefix Caching**: Menjaga bagian prompt statis (seperti System Prompt, Tool Declarations, dan Arsitektur Dasar) berada di awal (prefix) agar nilai Key-Value Attention dapat di-cache dan digunakan kembali.
- **Cache Hit Maximization**: Hindari memodifikasi bagian awal riwayat obrolan/system prompt secara acak agar tidak memicu *cache invalidation*.
- **Append-Only Context Strategy**: Tambahkan konteks baru di bagian akhir (*suffix*) untuk memastikan komputasi token sebelumnya tidak perlu dihitung ulang dari awal.
