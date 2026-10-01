---
name: media-optimization-workflow
description: >-
  Workflow optimasi upload media (gambar ke WebP ringan), skeleton loader UI, resource preloading, dan estimasi biaya (cost tracker).
  Gunakan skill ini untuk mengimplementasikan pipeline pengunggahan aset ringan, UI skeleton placeholder, dan strategi preloading non-blocking.
---

# Media Optimization & UI Performance Workflow

Skill ini menguraikan prosedur teknis optimasi pengunggahan gambar ke format WebP ringan, implementasi skeleton state, resource preload, dan script kalkulator/estimasi biaya (*cost script*).

---

## 1. Workflow Upload & Kompresi Gambar ke WebP (Client-Side & Lightweight)

Workflow ini mengubah gambar yang diunggah menjadi WebP terkompresi langsung menggunakan native HTML5 Canvas API tanpa memerlukan library berat.

```javascript
/**
 * Kompresi dan konversi file gambar ke format WebP ringan.
 * Mendukung OffscreenCanvas / Worker untuk zero-UI-jank.
 * @param {File} file - File gambar asli (JPG, PNG, dll.)
 * @param {Object} options - Konfigurasi kompresi
 * @param {number} options.maxWidth - Batas lebar maksimal (default: 1280px)
 * @param {number} options.maxHeight - Batas tinggi maksimal (default: 1280px)
 * @param {number} options.quality - Kualitas WebP dari 0.1 s/d 1.0 (default: 0.8)
 * @returns {Promise<Blob>}
 */
async function compressImageToWebP(file, { maxWidth = 1280, maxHeight = 1280, quality = 0.8 } = {}) {
  // 1. Validasi tipe file
  if (!file.type.startsWith('image/')) {
    throw new Error('File bukan gambar valid.');
  }

  // 2. Gunakan createImageBitmap & OffscreenCanvas jika tersedia (Non-blocking background thread)
  if (typeof OffscreenCanvas !== 'undefined' && typeof createImageBitmap === 'function') {
    const bitmap = await createImageBitmap(file);
    let { width, height } = bitmap;

    if (width > maxWidth || height > maxHeight) {
      const ratio = Math.min(maxWidth / width, maxHeight / height);
      width = Math.round(width * ratio);
      height = Math.round(height * ratio);
    }

    const offscreen = new OffscreenCanvas(width, height);
    const ctx = offscreen.getContext('2d', { alpha: true });
    ctx.drawImage(bitmap, 0, 0, width, height);
    bitmap.close();

    return await offscreen.convertToBlob({ type: 'image/webp', quality });
  }

  // 3. Fallback Canvas Standard
  return new Promise((resolve, reject) => {
    const img = new Image();
    const objectUrl = URL.createObjectURL(file);

    img.onload = () => {
      URL.revokeObjectURL(objectUrl);
      let { width, height } = img;

      if (width > maxWidth || height > maxHeight) {
        const ratio = Math.min(maxWidth / width, maxHeight / height);
        width = Math.round(width * ratio);
        height = Math.round(height * ratio);
      }

      const canvas = document.createElement('canvas');
      canvas.width = width;
      canvas.height = height;
      const ctx = canvas.getContext('2d', { alpha: true });
      ctx.drawImage(img, 0, 0, width, height);

      canvas.toBlob(
        (blob) => {
          if (blob) resolve(blob);
          else reject(new Error('Gagal mengompresi gambar ke WebP.'));
        },
        'image/webp',
        quality
      );
    };

    img.onerror = () => {
      URL.revokeObjectURL(objectUrl);
      reject(new Error('Gagal membaca file gambar.'));
    };

    img.src = objectUrl;
  });
}
```

---

## 2. Skeleton Loading Workflow (Tailwind CSS Base)
Menghindari layout shift (CLS) dan memberikan feedback visual instan saat aset sedang diproses atau diunduh.

### Implementasi Tailwind CSS Skeleton:
```html
<!-- Skeleton Image & Content Placeholder menggunakan Tailwind utility -->
<div class="animate-pulse flex flex-col space-y-3 p-4 bg-slate-800/80 rounded-2xl border border-slate-700/60 shadow-lg">
  <div class="w-full h-48 bg-slate-700/70 rounded-xl flex items-center justify-center">
    <!-- Icon SVG Placeholder (No Raw Emoji) -->
    <svg class="w-10 h-10 text-slate-500 animate-bounce" fill="none" stroke="currentColor" viewBox="0 0 24 24">
      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M4 16l4.586-4.586a2 2 0 012.828 0L16 16m-2-2l1.586-1.586a2 2 0 012.828 0L20 14m-6-6h.01M6 20h12a2 2 0 002-2V6a2 2 0 00-2-2H6a2 2 0 00-2 2v12a2 2 0 002 2z"></path>
    </svg>
  </div>
  <div class="h-4 bg-slate-700 rounded w-3/4"></div>
  <div class="h-3 bg-slate-700/80 rounded w-1/2"></div>
</div>
```

---

## 3. Frontend Iconography Standards
- **Aturan Ketat**: Tidak menggunakan emotikon teks biasa (emoji) pada komponen UI.
- **Standar**:
  - Gunakan **Inline SVG** (dengan utility Tailwind `w-5 h-5 fill-current text-indigo-400`).
  - Atau gunakan pustaka **FontAwesome** (seperti `<i class="fa-solid fa-file-arrow-up"></i>` / FontAwesome SVG).

---

## 4. Preload Strategy (Non-Blocking)
Optimasi pemuatan aset krusial (font, modul kritis, aset gambar hero) tanpa memperlambat First Contentful Paint (FCP):

- **Preload Kritis**: Gunakan `<link rel="preload" as="image" href="..." type="image/webp">` hanya untuk gambar di atas lipatan layar (above the fold).
- **Prefetch/Preconnect**: `<link rel="preconnect" href="https://api.domain.com">` untuk koneksi jaringan yang segera dipakai.
- **Lazy Loading**: Gunakan atribut native `loading="lazy"` dan `decoding="async"` untuk semua gambar di bawah lipatan layar.

---

## 5. Cost Calculation Script (Lightweight Token / API Cost Tracker)

Skrip ringan untuk menghitung estimasi biaya token input/output & komputasi secara real-time tanpa membebani runtime:

```javascript
/**
 * Lightweight Cost Estimator untuk Token & Komputasi
 */
class CostTracker {
  constructor(rates = { inputRatePer1M: 0.15, outputRatePer1M: 0.60 }) {
    this.rates = rates;
    this.totalInputTokens = 0;
    this.totalOutputTokens = 0;
  }

  recordUsage(inputTokens, outputTokens) {
    this.totalInputTokens += inputTokens;
    this.totalOutputTokens += outputTokens;
  }

  calculateCost() {
    const inputCost = (this.totalInputTokens / 1_000_000) * this.rates.inputRatePer1M;
    const outputCost = (this.totalOutputTokens / 1_000_000) * this.rates.outputRatePer1M;
    const totalCost = inputCost + outputCost;

    return {
      inputTokens: this.totalInputTokens,
      outputTokens: this.totalOutputTokens,
      totalCostUSD: Number(totalCost.toFixed(6)),
      formattedCost: `$${totalCost.toFixed(4)}`
    };
  }
}
```
