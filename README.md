# ctrovalidate-vue

**Reactive form validation for Vue 3.**

`ctrovalidate-vue` provides a `useCtrovalidate` composable that wraps [`ctrovalidate-core`](https://www.npmjs.com/package/ctrovalidate-core)'s validation engine with Vue 3's reactivity system. Supports `v-model`, watcher-based real-time validation, and automatic abort handling.

[![npm version](https://img.shields.io/npm/v/ctrovalidate-vue.svg)](https://www.npmjs.com/package/ctrovalidate-vue)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

---

## Installation

```bash
npm install ctrovalidate-vue ctrovalidate-core
```

**Requirements:** Vue ^3.0.0

---

## Quick Start

```vue
<script setup lang="ts">
import { useCtrovalidate } from 'ctrovalidate-vue';

interface LoginForm { email: string; password: string; }

const { values, errors, handleBlur, validateForm, isValidating } =
  useCtrovalidate<LoginForm>({
    initialValues: { email: '', password: '' },
    schema: { email: 'required|email', password: 'required|minLength:8' },
  });
</script>

<template>
  <form @submit.prevent="validateForm()">
    <input v-model="values.email" @blur="handleBlur('email')" />
    <span v-if="errors.email">{{ errors.email }}</span>

    <input type="password" v-model="values.password" @blur="handleBlur('password')" />
    <span v-if="errors.password">{{ errors.password }}</span>

    <button :disabled="Object.values(isValidating).some(Boolean)">Login</button>
  </form>
</template>
```

---

## API

```typescript
const {
  values,         // Reactive<T> — form state, v-model ready
  errors,         // Reactive — pre-initialized, string | undefined per field
  isDirty,        // Reactive — tracks touched fields
  isValidating,   // Reactive — tracks async validation
  isValid,        // boolean — overall form validity (Ref.unwrap)
  handleChange,   // (name, value) => void
  handleBlur,     // (name) => void
  validateField,  // (name) => Promise<boolean>
  validateForm,   // () => Promise<boolean>
  reset,          // (newValues?) => void
} = useCtrovalidate<T>({
  schema,              // Required: field-to-rules mapping
  initialValues,       // Optional: form defaults (default: {})
  validateOnBlur,      // Optional: validate on blur (default: true)
  validateOnChange,    // Optional: watch values, validate dirty fields (default: true)
  customRules,         // Optional: custom sync/async rule functions
  aliases,             // Optional: rule combination aliases
  messages,            // Optional: custom error messages
  locale,              // Optional: locale override for translator
});
```

---

## Validation Behavior

| Trigger | Condition | Action |
|---------|-----------|--------|
| `handleChange(name, value)` | `validateOnChange` | Updates value, marks dirty, validates |
| `handleBlur(name)` | `validateOnBlur` | Marks dirty, validates |
| `v-model` change | `validateOnChange` + `watch()` | Validates dirty fields |

When `validateOnChange` is enabled, a `watch()` with `{ deep: true }` on `values` re-validates any dirty field whose value changed.

---

## Abort Handling

Each field has its own `AbortController`. Re-validating a field aborts any in-flight async rule. All controllers are aborted on `onUnmounted`.

---

## Related Packages

- **[ctrovalidate-core](https://www.npmjs.com/package/ctrovalidate-core)** — Validation engine
- **[ctrovalidate-browser](https://www.npmjs.com/package/ctrovalidate-browser)** — Vanilla JS DOM integration
- **[ctrovalidate-react](https://www.npmjs.com/package/ctrovalidate-react)** — React hook
- **[ctrovalidate-svelte](https://www.npmjs.com/package/ctrovalidate-svelte)** — Svelte stores
- **[ctrovalidate-next](https://www.npmjs.com/package/ctrovalidate-next)** — Next.js server actions

---

## License

MIT © [Ctrotech](https://github.com/ctrotech-tutor)

Full documentation: https://ctrovalidate.vercel.app
