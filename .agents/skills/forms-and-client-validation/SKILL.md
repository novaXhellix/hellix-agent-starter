---
name: forms-and-client-validation
description: >-
  Arsitektur validasi form modern, schema validator ringan, debounce input, dan status UI interaktif tanpa modal.
  Gunakan skill ini untuk membangun formulir input data, validasi field real-time, dan sanitasi data input.
---

# Modern Form Architecture & Client-Side Validation

Skill ini memberikan panduan membangun formulir yang responsif, terintegrasi dengan Tailwind CSS, dan tervalidasi secara presisi.

---

## 1. Lightweight Schema-based Form Validator

```javascript
/**
 * Validator form ringan berbasis aturan deklaratif
 */
class FormValidator {
  constructor(rules = {}) {
    this.rules = rules;
  }

  validate(formData) {
    const errors = {};
    let isValid = true;

    for (const [field, ruleSet] of Object.entries(this.rules)) {
      const value = formData[field];

      if (ruleSet.required && (value === undefined || value === null || value === '')) {
        errors[field] = ruleSet.requiredMessage || `${field} wajib diisi.`;
        isValid = false;
        continue;
      }

      if (ruleSet.minLength && String(value).length < ruleSet.minLength) {
        errors[field] = `Minimal ${ruleSet.minLength} karakter.`;
        isValid = false;
      }

      if (ruleSet.pattern && !ruleSet.pattern.test(String(value))) {
        errors[field] = ruleSet.patternMessage || `Format ${field} tidak valid.`;
        isValid = false;
      }

      if (ruleSet.custom) {
        const customErr = ruleSet.custom(value, formData);
        if (customErr) {
          errors[field] = customErr;
          isValid = false;
        }
      }
    }

    return { isValid, errors };
  }
}
```

---

## 2. Debounced Input Handler (Real-Time Validation)

```javascript
/**
 * Utilitas Debounce untuk pencarian atau validasi field on-the-fly
 */
function debounce(func, delay = 300) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => func(...args), delay);
  };
}
```
