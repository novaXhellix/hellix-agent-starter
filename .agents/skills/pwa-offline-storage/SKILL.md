---
name: pwa-offline-storage
description: >-
  Arsitektur Progressive Web App (PWA), Service Worker caching, dan manajemen offline data dengan IndexedDB.
  Gunakan skill ini untuk mengimplementasikan fitur offline-first, background sync, dan caching aset statis.
---

# PWA & Offline-First Storage Architecture

Skill ini mengatur implementasi aplikasi web modern agar dapat berjalan secara offline dan menyimpan data terstruktur di client.

---

## 1. Lightweight IndexedDB Key-Value Store

```javascript
/**
 * Asynchronous Key-Value Store berbasis IndexedDB Native
 */
class OfflineStorage {
  constructor(dbName = 'zencode_db', storeName = 'keyval') {
    this.dbName = dbName;
    this.storeName = storeName;
    this.dbPromise = this.initDB();
  }

  initDB() {
    return new Promise((resolve, reject) => {
      const request = indexedDB.open(this.dbName, 1);
      request.onupgradeneeded = (e) => {
        const db = e.target.result;
        if (!db.objectStoreNames.contains(this.storeName)) {
          db.createObjectStore(this.storeName);
        }
      };
      request.onsuccess = () => resolve(request.result);
      request.onerror = () => reject(request.error);
    });
  }

  async get(key) {
    const db = await this.dbPromise;
    return new Promise((resolve, reject) => {
      const tx = db.transaction(this.storeName, 'readonly');
      const req = tx.objectStore(this.storeName).get(key);
      req.onsuccess = () => resolve(req.result);
      req.onerror = () => reject(req.error);
    });
  }

  async set(key, value) {
    const db = await this.dbPromise;
    return new Promise((resolve, reject) => {
      const tx = db.transaction(this.storeName, 'readwrite');
      const req = tx.objectStore(this.storeName).put(value, key);
      req.onsuccess = () => resolve();
      req.onerror = () => reject(req.error);
    });
  }

  async delete(key) {
    const db = await this.dbPromise;
    return new Promise((resolve, reject) => {
      const tx = db.transaction(this.storeName, 'readwrite');
      const req = tx.objectStore(this.storeName).delete(key);
      req.onsuccess = () => resolve();
      req.onerror = () => reject(req.error);
    });
  }
}
```

---

## 2. Service Worker Cache Registration

```javascript
/**
 * Registrasi Service Worker untuk PWA Caching
 */
function registerServiceWorker() {
  if ('serviceWorker' in navigator) {
    window.addEventListener('load', () => {
      navigator.serviceWorker.register('/sw.js')
        .then(reg => console.log('SW terdaftar:', reg.scope))
        .catch(err => console.warn('SW registrasi gagal:', err));
    });
  }
}
```
