---
name: modern-api-data-fetching
description: >-
  Arsitektur HTTP Client modern, interceptor, retry mechanism dengan exponential backoff, dan strategi client-side caching (SWR/Stale-While-Revalidate).
  Gunakan skill ini saat membuat modul integrasi API, sinkronisasi data backend, atau mengelola state server.
---

# Modern API & Resilient Data Fetching

Skill ini menyediakan implementasi API client yang tangguh, aman, dan efisien tanpa bergantung pada framework berat.

---

## 1. Resilient API Client dengan Exponential Backoff Retry

```javascript
/**
 * Universal Fetch Wrapper dengan Timeout, Interceptor, dan Exponential Backoff Retry.
 */
class ApiClient {
  constructor({ baseUrl = '', timeoutMs = 15000, defaultHeaders = {} } = {}) {
    this.baseUrl = baseUrl.replace(/\/$/, '');
    this.timeoutMs = timeoutMs;
    this.defaultHeaders = defaultHeaders;
  }

  async request(endpoint, options = {}, retries = 3, backoffDelay = 1000) {
    const url = `${this.baseUrl}/${endpoint.replace(/^\//, '')}`;
    const controller = new AbortController();
    const timeoutId = setTimeout(() => controller.abort(), this.timeoutMs);

    const config = {
      ...options,
      signal: controller.signal,
      headers: {
        'Content-Type': 'application/json',
        ...this.defaultHeaders,
        ...options.headers
      }
    };

    try {
      const response = await fetch(url, config);
      clearTimeout(timeoutId);

      if (!response.ok) {
        if (response.status >= 500 && retries > 0) {
          await new Promise((r) => setTimeout(r, backoffDelay));
          return this.request(endpoint, options, retries - 1, backoffDelay * 2);
        }
        const errorBody = await response.json().catch(() => ({}));
        throw new Error(errorBody.message || `HTTP Error: ${response.status}`);
      }

      return await response.json();
    } catch (err) {
      clearTimeout(timeoutId);
      if (err.name === 'AbortError') {
        throw new Error(`Request timeout (${this.timeoutMs}ms) ke: ${url}`);
      }
      if (retries > 0) {
        await new Promise((r) => setTimeout(r, backoffDelay));
        return this.request(endpoint, options, retries - 1, backoffDelay * 2);
      }
      throw err;
    }
  }

  get(endpoint, options = {}) {
    return this.request(endpoint, { ...options, method: 'GET' });
  }

  post(endpoint, body, options = {}) {
    return this.request(endpoint, { ...options, method: 'POST', body: JSON.stringify(body) });
  }

  put(endpoint, body, options = {}) {
    return this.request(endpoint, { ...options, method: 'PUT', body: JSON.stringify(body) });
  }

  delete(endpoint, options = {}) {
    return this.request(endpoint, { ...options, method: 'DELETE' });
  }
}
```

---

## 2. In-Memory SWR Cache (Stale-While-Revalidate)

```javascript
/**
 * Lightweight SWR Cache Manager
 */
class SwrCache {
  constructor(ttlMs = 60000) {
    this.cache = new Map();
    this.ttlMs = ttlMs;
  }

  async execute(key, fetcher, onUpdate) {
    const cached = this.cache.get(key);
    const now = Date.now();

    // Jika ada di cache, kembalikan data instan (Zero latency)
    if (cached) {
      // Revalidasi di background jika sudah stale
      if (now - cached.timestamp > this.ttlMs) {
        fetcher().then((freshData) => {
          this.cache.set(key, { data: freshData, timestamp: Date.now() });
          if (typeof onUpdate === 'function') onUpdate(freshData);
        }).catch(console.error);
      }
      return cached.data;
    }

    // Jika belum ada, fetch baru
    const freshData = await fetcher();
    this.cache.set(key, { data: freshData, timestamp: Date.now() });
    return freshData;
  }
}
```
