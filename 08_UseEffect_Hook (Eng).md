# 8_UseEffect_Hook — Full Step-by-Step Document (how it works, how to use, why/why not, examples & use cases)

---

## 1 — One-line summary

`useEffect` is a React hook that runs side-effects after a component renders (mount + updates) and optionally cleans them up, controlled by a dependency array.

---

## 2 — What it **does** (how it works) — step by step

1. You call `useEffect(effectFn, deps)` inside a functional component.
2. After React paints the UI, it schedules `effectFn()` to run.
3. If `deps` is omitted, the effect runs after every render.
4. If `deps` is `[]`, the effect runs once after mount.
5. If `deps` is `[a, b]`, the effect runs after mount and whenever any dependency’s value/reference changes.
6. If `effectFn` returns a function (`cleanup`), React calls `cleanup()` before the next effect run and when the component unmounts.
7. React compares dependency values by reference for objects/arrays and by value for primitives to decide re-runs.

---

## 3 — Syntax & basic forms

```js
import React, { useEffect } from 'react';

// run after every render
useEffect(() => {
  // effect
});

// run once (mount)
useEffect(() => {
  // effect
}, []);

// run when dep changes
useEffect(() => {
  // effect using dep
  return () => {
    // cleanup
  };
}, [dep]);
```

---

## 4 — How to use it (practical patterns)

* **Mount-only setup/teardown** (e.g., subscribe once): `useEffect(..., [])`.
* **Reactive effect** (e.g., update title when state changes): `useEffect(..., [state])`.
* **Async data fetching**: create an inner async function or use `AbortController` — **don’t** make the effect callback `async`.
* **Cleanup**: return a function to remove listeners, cancel timers, abort fetches.
* **Multiple concerns**: split into multiple `useEffect` calls — each effect should do one thing.

---

## 5 — Why use `useEffect` (benefits)

* Keeps side-effects out of the render body (pure render) — easier reasoning and predictable rendering.
* Lets you declaratively express “run this when X changes” (dependency array).
* Provides automatic cleanup to avoid leaks (timers, subscriptions).
* Mirrors lifecycle behavior (mount, update, unmount) in functional components.
* Encourages separation of concerns: multiple small effects instead of big lifecycle methods.

---

## 6 — Why **not** use `useEffect` (when it’s the wrong tool)

* **Heavy synchronous computations** belong to memoization (`useMemo`) or moved outside render — not `useEffect`.
* **Derived UI output** that can be computed during render should not be in effects (put it in render or memo).
* **If you need to read/modify DOM before paint** (e.g., measure and synchronously change layout), use `useLayoutEffect` instead.
* **Overuse for things that can be purely declarative** (e.g., state derived from props) leads to complexity and bugs.
* **Putting unstable object/array literals directly in deps** causes constant re-runs — prefer `useMemo`/`useCallback`.

---

## 7 — Common pitfalls & how to fix them

* **Missing dependencies** → stale closures: include all used variables in `deps` or justify with comments and refs.
* **Infinite loops** → effect sets state that’s also in `deps` without guard: add conditions or restructure.
* **Object/array identity** → memoize or lift state so deps don’t change every render.
* **Async race conditions** → use `AbortController` or cancellation flags to ignore late responses.
* **Using `async` directly for effect** → don’t; wrap async logic inside.

---

## 8 — Comparisons (short)

* `useEffect` vs `useLayoutEffect`: `useEffect` runs after paint (non-blocking), `useLayoutEffect` runs before paint (blocking) for layout reads/writes.
* `useEffect` vs `useMemo`/`useCallback`: effects run side operations; memo/callback return pure values/functions for render optimization.

---

## 9 — Real examples (copy-paste ready)

### A — Fetch data with cancellation

```js
import { useEffect, useState } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();
    const load = async () => {
      try {
        const res = await fetch(`/api/users/${userId}`, { signal: controller.signal });
        if (!res.ok) throw new Error('Fetch failed');
        const data = await res.json();
        setUser(data);
      } catch (err) {
        if (err.name !== 'AbortError') setError(err);
      }
    };
    load();
    return () => controller.abort(); // cancel on userId change or unmount
  }, [userId]);

  // render...
}
```

### B — Event listener with cleanup

```js
useEffect(() => {
  function onResize() {
    console.log(window.innerWidth);
  }
  window.addEventListener('resize', onResize);
  return () => window.removeEventListener('resize', onResize);
}, []); // mount/unmount
```

### C — Debounced search

```js
useEffect(() => {
  const id = setTimeout(() => {
    doSearch(query);
  }, 500);
  return () => clearTimeout(id); // reset timeout if query changes
}, [query]);
```

### D — Custom hook using useEffect

```js
function useOnlineStatus() {
  const [online, setOnline] = useState(navigator.onLine);
  useEffect(() => {
    const onOnline = () => setOnline(true);
    const onOffline = () => setOnline(false);
    window.addEventListener('online', onOnline);
    window.addEventListener('offline', onOffline);
    return () => {
      window.removeEventListener('online', onOnline);
      window.removeEventListener('offline', onOffline);
    };
  }, []);
  return online;
}
```

---

## 10 — Practical use cases (where `useEffect` is appropriate)

* Data fetching on mount or when an ID/param changes.
* Subscribing/unsubscribing to sockets, event emitters, or store listeners.
* Timers (setInterval/setTimeout) with cleanup.
* Syncing external systems (localStorage, analytics, document title).
* Debouncing input or search requests.
* DOM side-effects that don’t need pre-paint adjustments (animations started after paint).

---

## 11 — When to prefer alternatives

* Use `useLayoutEffect` if you must measure and synchronously adjust DOM before the browser paints to avoid flicker.
* Use `useMemo`/`useCallback` for expensive pure computations or stable function/object identities, not `useEffect`.
* Compute derived state in render/memo rather than setting state inside an effect when possible.

---

## 12 — Best practices checklist before writing an effect

* [ ] Do I need a side-effect (network, I/O, timer, subscription)?
* [ ] Which values from props/state are used? Add them to `deps`.
* [ ] Does the effect need cleanup? If yes, return a cleanup function.
* [ ] Could it cause an infinite loop? Avoid writing state updates that always change deps.
* [ ] Are any objects/arrays in deps stable? If not, memoize them.
* [ ] Would `useLayoutEffect` be more appropriate (layout/read-before-paint)? If yes, use it carefully.

---

## 13 — Quick debugging tips

* `console.log` inside effect and cleanup to see when they run.
* Use React DevTools to inspect renders.
* Enable ESLint rule `react-hooks/exhaustive-deps` to find missing deps.
* Temporarily reduce effect to minimal code to isolate problem.

---

## 14 — Short printable cheat-sheet (copy)

* `useEffect(() => { ... })` → runs after every render.
* `useEffect(() => { ... }, [])` → mount only.
* `useEffect(() => { ... }, [a, b])` → run when `a` or `b` changes.
* `useEffect(() => { return () => { /* cleanup */ } }, deps)` → cleanup before next effect/unmount.
* Don’t `async` the top-level effect callback — put async inside.
* Add all used variables to `deps` or use `useRef`/`useCallback`/`useMemo` intentionally.

---

## 15 — Mini real project use case (end-to-end)

**Feature:** Search page that fetches results for `query`, debounced 500ms, cancels previous fetch when `query` changes.

* `useState` for `query`, `results`, `loading`.
* `useEffect` with `[query]` + `setTimeout` to debounce.
* Inside effect start fetch with `AbortController`.
* Cleanup clears timeout and aborts previous fetch on change/unmount.
  This pattern provides responsive UI, reduced network calls, and safe cancellation.

---

## 16 — Exercises to practice

1. Build a `Clock` using `useEffect` and `setInterval`, cleaning up on unmount.
2. Create a `useFetch(url)` custom hook that fetches data and cancels on url change.
3. Implement a debounced search input that only queries the server 500ms after the user stops typing.
4. Create a component that measures an element’s width (`ref`) using `useLayoutEffect`, then logs it.

---

## 17 — TL;DR (final summary)

Use `useEffect` to run side-effects after render and clean them up; control when it runs with the dependency array, avoid missing deps and unstable references, use `useLayoutEffect` only for pre-paint DOM work, and split effects by concern for clarity and maintainability.

---
