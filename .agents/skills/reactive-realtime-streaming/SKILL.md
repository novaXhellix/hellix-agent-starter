---
name: reactive-realtime-streaming
description: >-
  Penanganan Server-Sent Events (SSE), WebSocket real-time connection, dan streaming response AI per-token (Typewriter effect).
  Gunakan skill ini saat mengimplementasikan chat interaktif, live metrics, atau token streaming AI.
---

# Reactive Real-Time & Streaming Architecture

Skill ini mengatur pola koneksi live dan rendering respons teks streaming token-by-token secara halus.

---

## 1. AI Token Stream Consumer (SSE / ReadableStream)

```javascript
/**
 * Membaca token stream dari endpoint AI secara real-time.
 * @param {string} url - Endpoint URL
 * @param {Object} body - Payload request
 * @param {Function} onToken - Callback saat potongan token baru diterima
 * @param {Function} onComplete - Callback saat stream selesai
 */
async function streamAiResponse(url, body, onToken, onComplete) {
  const response = await fetch(url, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(body)
  });

  if (!response.ok || !response.body) {
    throw new Error(`Streaming request failed: ${response.statusText}`);
  }

  const reader = response.body.getReader();
  const decoder = new TextDecoder('utf-8');
  let fullText = '';

  while (true) {
    const { done, value } = await reader.read();
    if (done) break;

    const chunk = decoder.decode(value, { stream: true });
    // Parse format SSE ("data: ...") atau raw text chunk
    const lines = chunk.split('\n');
    for (const line of lines) {
      const trimmed = line.trim();
      if (trimmed.startsWith('data: ')) {
        const payload = trimmed.replace('data: ', '');
        if (payload === '[DONE]') continue;
        try {
          const parsed = JSON.parse(payload);
          const token = parsed.delta || parsed.text || '';
          fullText += token;
          onToken(token, fullText);
        } catch {
          fullText += payload;
          onToken(payload, fullText);
        }
      } else if (trimmed) {
        fullText += trimmed;
        onToken(trimmed, fullText);
      }
    }
  }

  if (typeof onComplete === 'function') onComplete(fullText);
  return fullText;
}
```

---

## 2. Reconnecting WebSocket Manager

```javascript
/**
 * WebSocket dengan auto-reconnect dan event dispatching
 */
class LiveSocket {
  constructor(url, reconnectInterval = 3000) {
    this.url = url;
    this.reconnectInterval = reconnectInterval;
    this.listeners = new Map();
    this.connect();
  }

  connect() {
    this.ws = new WebSocket(this.url);

    this.ws.onmessage = (event) => {
      try {
        const data = JSON.parse(event.data);
        const handlers = this.listeners.get(data.type) || [];
        handlers.forEach(fn => fn(data.payload));
      } catch {
        // Plain text message
      }
    };

    this.ws.onclose = () => {
      setTimeout(() => this.connect(), this.reconnectInterval);
    };
  }

  on(type, callback) {
    if (!this.listeners.has(type)) this.listeners.set(type, []);
    this.listeners.get(type).push(callback);
  }

  send(type, payload) {
    if (this.ws.readyState === WebSocket.OPEN) {
      this.ws.send(JSON.stringify({ type, payload }));
    }
  }
}
```
