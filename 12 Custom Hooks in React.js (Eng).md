# Custom Hooks in React — Step-by-step guide (reusable logic)

— custom hooks are one of React’s best tools for sharing *behavior* (not UI) across components. I’ll walk you from the basics to practical patterns, examples (plain JS + a couple TypeScript snippets), testing tips, and common pitfalls.

---

## 1) What a custom hook is (short)

A **custom hook** is a JavaScript function whose name starts with `use` and that may call other hooks (like `useState`, `useEffect`, etc.). It encapsulates reusable stateful logic so multiple components can share that logic without duplicating code.

Key rules:

* Name must start with `use`.
* Call hooks only at top-level (no conditional or loop).
* Only call hooks from React function components or other hooks.

---

## 2) When to use custom hooks (use cases)

* **Data fetching** (e.g., `useFetch`, `useQuery`).
* **Form state & validation** (e.g., `useForm`).
* **Local storage syncing** (e.g., `useLocalStorage`).
* **Event listeners** (e.g., `useEventListener`).
* **Debounce or throttling values** (e.g., `useDebouncedValue`).
* **Shared derived state** (e.g., `usePagination`, `useAuth`).
* **Abstracting effects & cleanup** (timers, subscriptions).
* **Composing other hooks** into higher-level behaviors.

If you find components repeating the same `useState/useEffect` pattern — extract a hook.

---

## 3) Minimal example: `useToggle`

A tiny, commonly used utility.

```jsx
// useToggle.js
import { useCallback, useState } from "react";

export default function useToggle(initial = false) {
  const [on, setOn] = useState(Boolean(initial));
  const toggle = useCallback(() => setOn(v => !v), []);
  const setTrue = useCallback(() => setOn(true), []);
  const setFalse = useCallback(() => setOn(false), []);
  return { on, toggle, setTrue, setFalse };
}
```

Usage:

```jsx
import useToggle from "./useToggle";

function LikeButton() {
  const { on, toggle } = useToggle(false);
  return <button onClick={toggle}>{on ? "Liked" : "Like"}</button>;
}
```

---

## 4) Real-world example 1 — `useFetch` (with cleanup & abort)

A reusable fetch hook that handles loading, data, error, cancellation.

```jsx
// useFetch.js
import { useEffect, useState } from "react";

export default function useFetch(url, options = null, deps = []) {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    if (!url) return; // no-op if no url
    const ac = new AbortController();
    const signal = ac.signal;
    let mounted = true;

    setLoading(true);
    setError(null);
    setData(null);

    fetch(url, { ...options, signal })
      .then(async res => {
        if (!res.ok) throw new Error(`${res.status} ${res.statusText}`);
        const json = await res.json();
        if (mounted) setData(json);
      })
      .catch(err => {
        if (err.name === "AbortError") return;
        if (mounted) setError(err);
      })
      .finally(() => {
        if (mounted) setLoading(false);
      });

    return () => {
      mounted = false;
      ac.abort();
    };
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [url, ...(deps || [])]);

  return { data, error, loading };
}
```

Usage:

```jsx
import useFetch from "./useFetch";

function UsersList() {
  const { data, loading, error } = useFetch("/api/users");

  if (loading) return <div>Loading…</div>;
  if (error) return <div>Error: {error.message}</div>;
  return (
    <ul>
      {data?.map(u => <li key={u.id}>{u.name}</li>)}
    </ul>
  );
}
```

Notes:

* Accepts `deps` to re-run the effect if needed.
* Uses `AbortController` to cancel fetch on unmount.

---

## 5) Real-world example 2 — `useLocalStorage`

Sync state to `localStorage`.

```jsx
// useLocalStorage.js
import { useCallback, useEffect, useState } from "react";

export default function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const raw = window.localStorage.getItem(key);
      return raw !== null ? JSON.parse(raw) : initialValue;
    } catch {
      return initialValue;
    }
  });

  useEffect(() => {
    try {
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch {}
  }, [key, value]);

  const remove = useCallback(() => {
    try {
      window.localStorage.removeItem(key);
      setValue(undefined);
    } catch {}
  }, [key]);

  return [value, setValue, remove];
}
```

Usage:

```jsx
const [theme, setTheme] = useLocalStorage("theme", "light");
```

---

## 6) Composition: Build complex hooks from small hooks

Custom hooks can call other hooks. Compose small focused hooks to create richer behavior.

Example: `useDebouncedLocalStorage` uses `useLocalStorage` and `useDebouncedValue`.

---

## 7) Helpful utility hooks (short snippets)

* `usePrevious` — get previous prop/state.
* `useEventListener` — add/remove window or element listeners.
* `useDebouncedValue` — debounce a changing value.

`usePrevious`:

```jsx
import { useEffect, useRef } from "react";

export function usePrevious(value) {
  const ref = useRef();
  useEffect(() => { ref.current = value; }, [value]);
  return ref.current;
}
```

`useDebouncedValue`:

```jsx
import { useEffect, useState } from "react";

export function useDebouncedValue(value, delay = 300) {
  const [debounced, setDebounced] = useState(value);
  useEffect(() => {
    const t = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(t);
  }, [value, delay]);
  return debounced;
}
```

---

## 8) TypeScript example (strongly typed `useLocalStorage`)

```ts
// useLocalStorage.ts
import { useEffect, useState } from "react";

export default function useLocalStorage<T>(key: string, initialValue: T) {
  const [value, setValue] = useState<T>(() => {
    try {
      const raw = localStorage.getItem(key);
      return raw ? (JSON.parse(raw) as T) : initialValue;
    } catch {
      return initialValue;
    }
  });

  useEffect(() => {
    try {
      localStorage.setItem(key, JSON.stringify(value));
    } catch {}
  }, [key, value]);

  return [value, setValue] as const;
}
```

---

## 9) Testing custom hooks

Use `@testing-library/react-hooks` or `@testing-library/react`'s `renderHook` utilities.

Example with `@testing-library/react-hooks` (pseudo):

```js
import { renderHook, act } from '@testing-library/react-hooks';
import useToggle from './useToggle';

test('toggle changes state', () => {
  const { result } = renderHook(() => useToggle(false));
  expect(result.current.on).toBe(false);
  act(() => result.current.toggle());
  expect(result.current.on).toBe(true);
});
```

For `useFetch`, mock `fetch` (jest) and assert loading/data/error state transitions. Clean up unmount tests to ensure abort works.

---

## 10) Performance & best practices

* **Keep hooks focused.** Prefer small, single-responsibility hooks (compose them for higher-level behavior).
* **Memoize** handlers with `useCallback` when returning functions from hooks, so consumers don’t trigger unnecessary re-renders.
* **Return stable references** when possible (e.g., memoize an API: `return useMemo(() => ({...}), [...])`).
* **Avoid heavy calculations inside render** — use `useMemo` or compute inside hook but memoize result.
* **Be careful with dependencies.** Document which dependencies the hook expects as input. Use exhaustive-deps for effects inside hooks.
* **Do not put components inside hooks.** Hooks should return values, state, and functions — not JSX.

---

## 11) Common mistakes & pitfalls

* Naming not starting with `use` → React won’t apply the rules-of-hooks checks.
* Calling hooks conditionally (inside `if`) → bugs.
* Exposing changing function references → cause child renders. Use `useCallback`.
* Storing non-serializable stuff in `localStorage`. Serialize safely.
* Over-abstracting: if only one component uses the code, abstraction can add unnecessary indirection.

---

## 12) When not to use a custom hook

* If logic is UI markup or rendering-specific — prefer a component (HOC or render prop) or just inline the JSX.
* If you need to share markup *and* behavior: consider a wrapper component.
* If the logic is trivial and only used once — avoid premature abstraction.

---

## 13) Example: full small project structure

```
/hooks
  useFetch.js
  useLocalStorage.js
  useToggle.js
/components
  UsersList.jsx
  ThemeSwitcher.jsx
```

`UsersList` uses `useFetch`; `ThemeSwitcher` uses `useLocalStorage` + `useToggle`. Clear separation.

---

## 14) Checklist before extracting a hook

* Is the logic repeated in 2+ places?
* Is the logic stateful or uses hooks (`useState`, `useEffect`)?
* Can it be tested independently?
* Are inputs/outputs clear and minimal?
* Does it adhere to rules-of-hooks (no conditionals, top-level only)?

---

## 15) Quick cheat sheet (one-line recipes)

* Data-fetching: `useFetch(url, options, deps)`
* Persisted state: `useLocalStorage(key, initial)`
* Debounce: `useDebouncedValue(value, 300)`
* Event listeners: `useEventListener('resize', handler)`
* Toggle: `useToggle(initial)`

---
