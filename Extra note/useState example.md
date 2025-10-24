
---

# 🔥 সম্পূর্ণ UseState Masterclass (বাংলায়)

---

## ১) বেসিক: Primitive state (Number/String/Boolean)

### কোড:

```jsx
import React, { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);
  const [label, setLabel] = useState("Clicks");

  return (
    <div>
      <h1>{label}: {count}</h1>
      <button onClick={() => setCount(c => c + 1)}>Increase</button>
      <button onClick={() => setCount(0)}>Reset</button>
      <button onClick={() => setLabel("Total Clicks")}>Rename</button>
    </div>
  );
}
```

### বিশদ ব্যাখ্যা:

* `useState(0)`: ইনিশিয়াল ভ্যালু 0। primitive হলে set-করে সরাসরি রিপ্লেস হয়।
* `setCount(c => c + 1)` — **functional updater** ব্যবহার করা হয়েছে; এটা future-proof (যখন একাধিক update একই সময় হবে)।
* `setCount(0)` → সরাসরি নতুন primitive সেট করা। ভালো ও সহজ।

### না করলে কী সমস্যা:

* যদি তুমি `setCount(count + 1)` করো এবং একই ইভেন্ট হ্যান্ডলায় একাধিক বার কল করো, কখনো কখনো stale value আসতে পারে; functional updater এ void।

---

## ২) Object state — shallow copy প্রয়োজন

### কোড:

```jsx
function UserCard() {
  const [user, setUser] = useState({ name: "Sakib", age: 25, online: false });

  const birthday = () => setUser(prev => ({ ...prev, age: prev.age + 1 }));
  const toggleOnline = () => setUser(prev => ({ ...prev, online: !prev.online }));
  const rename = () => setUser(prev => ({ ...prev, name: "Md. Sakib" }));

  return (
    <div>
      <h2>{user.name} — {user.age} — {user.online ? "Online" : "Offline"}</h2>
      <button onClick={birthday}>Birthday</button>
      <button onClick={toggleOnline}>Toggle</button>
      <button onClick={rename}>Rename</button>
    </div>
  );
}
```

### কেন spread (`{ ...prev }`) লাগছে:

* React state রি-রেন্ডার ট্রিগার করে যখন state এর reference বদলায়। যদি তুমি সরাসরি `prev.age = prev.age + 1` করো, reference একই থাকবে → React বুঝবে না কিছু বদলেছে (anti-pattern)।
* `...prev` দিয়ে একটি **নতুন object** তৈরি হয়—React নিশ্চিতভাবে re-render করে।

### ভুল উদাহরণ:

```js
// ❌ ভুল
user.age += 1;
setUser(user);
```

* direct mutation করলে unpredictability — avoid.

---

## ৩) Array state — immutable updates

### কোড:

```jsx
function TodoApp() {
  const [todos, setTodos] = useState(["Buy milk", "Learn React"]);

  const add = (text) => setTodos(prev => [...prev, text]);
  const removeAt = (index) => setTodos(prev => prev.filter((_, i) => i !== index));
  const replaceAt = (index, text) => setTodos(prev => prev.map((t, i) => i === index ? text : t));

  return (
    <div>
      <ul>{todos.map((t, i) => <li key={i}>{t}</li>)}</ul>
    </div>
  );
}
```

### গুরুত্বপূর্ণ টিপস:

* কখনো `.push()` দিয়ে মূল array মিউটেট করা যাবে না যদি সেটা মূল state হয়। কিন্তু `const copy = [...todos]; copy.push(x); setTodos(copy)` — এটি কাজ করবে কারণ তুমি copy মিউটেট করছ। কিন্তু idiomatic হচ্ছে `setTodos(prev => [...prev, x])`।
* `key` হিসেবে index ব্যবহার করলে reorder হলে সমস্যা হতে পারে—প্রাকটিক্যালly আইটেম id দিয়ে key দাও।

---

## ৪) Nested object updates (deep updates)

### কোড:

```jsx
const [profile, setProfile] = useState({
  name: "Sakib",
  address: { city: "Dhaka", zip: "1207" },
  preferences: { theme: "dark" }
});

function updateCity(newCity){
  setProfile(prev => ({
    ...prev,
    address: { ...prev.address, city: newCity }
  }));
}
```

### কেন এভাবে:

* spread operator একটা **shallow copy** করে। তাই nested object হলে nested অংশ আলাদাভাবে copy করতে হবে, নাহলে nested reference একই থাকবে এবং mutation ঘটতে পারে।

### বিকল্পে `immer` ব্যবহার:

* যদি nested structures বড় হয়, `immer` দিয়ে লিখতে পারে — `produce(prev, draft => { draft.address.city = newCity })` — এটা readable ও bug-resistant।

---

## ৫) Lazy initial state (expensive init)

### কোড:

```jsx
function ExpensiveInit() {
  const [data, setData] = useState(() => {
    // heavy calculation — only runs once
    const big = Array.from({ length: 1_000_000 }, (_, i) => i);
    return big;
  });

  return <div>Len: {data.length}</div>;
}
```

### ব্যাখ্যা:

* `useState(() => expensive())` → initializer function, যাতে expensive কাজ কেবল প্রথম রেন্ডারে হয়; না হলে rerender এ হেভি কাজ চলতে পারে।

---

## ৬) Multiple setState calls inside same handler (batching & ordering)

### উদাহরণ (সমস্যা দেখাবো):

```jsx
function MultiUpdate() {
  const [count, setCount] = useState(0);

  const bad = () => {
    setCount(count + 1);
    setCount(count + 1);
    // result: increment by 1 (because count is same in both calls)
  };

  const good = () => {
    setCount(c => c + 1);
    setCount(c => c + 1);
    // result: increment by 2
  };

  return <div> {count} </div>;
}
```

### ব্যাখ্যা:

* React may batch state updates; calling `setCount(count + 1)` twice uses same `count` value → ends up +1.
* functional updater uses latest prev value sequentially → +2. তাই when multiple updates based on previous, always use functional form.

---

## ৭) Stale closures problem (common pitfall with setInterval/useEffect)

### সমস্যা উদাহরণ:

```jsx
function Timer() {
  const [sec, setSec] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setSec(sec + 1); // ❌ closes over initial sec value (stale)
    }, 1000);
    return () => clearInterval(id);
  }, []); // runs once
}
```

### সমস্যা ব্যাখ্যা:

* ফাংশনাল কম্পোনেন্টে closure থাকে — setInterval callback প্রথম render-এ থাকা `sec` ধরেই রাখে। ফলে এই কোড চলবে না ঠিক মতো।

### সঠিক উপায়:

```jsx
useEffect(() => {
  const id = setInterval(() => {
    setSec(s => s + 1); // ✅ functional updater avoids stale closure
  }, 1000);
  return () => clearInterval(id);
}, []);
```

---

## ৮) Reset to initial state & preserving initializer (object ref)

### কোড:

```jsx
const initial = { a:1, b:2 };
const [state, setState] = useState(initial);

// reset:
setState(initial); // careful: if initial is same ref, React may not rerender
// better:
setState({ ...initial }); // new object ensures rerender
```

### টিপ:

* যদি `initial` একটি constant object হয় এবং একই রেফারেন্স ব্যবহার করো, React কখনো re-render নাও করতে পারে কারণ shallow compare দেখলে একই ref। তাই copy করে দিন।

---

## ৯) When to split state vs keep combined object

### উদাহরণ ১ — split:

```js
const [name, setName] = useState("");
const [age, setAge] = useState(0);
```

### উদাহরণ ২ — combined:

```js
const [form, setForm] = useState({ name: "", age: 0 });
```

### কবে কোনটা?

* যদি দুটো ভিন্নভাবে আপডেট হয় এবং independence থাকে → split is simpler and may be marginally faster (smaller updates).
* যদি fields logically belong together (single form object), combined makes updates easier to pass around.
* performance-wise difference small; prefer readability & practicality.

---

## ১০) Performance considerations (re-renders, referential equality)

### বিষয়গুলো:

* `useState` যখন আপডেট হয় → component rerenders। বেশি granular state (split) হলে ছোট অংশ change হলে কম রerender? না, parent component rerender হয়। But splitting can avoid re-computations inside child via props memoization.
* Avoid creating new object/array in render unless necessary — e.g. passing inline objects as props breaks memoization:

  ```jsx
  <Child options={{ a:1 }} /> // creates new object every render
  ```

  Instead memoize:

  ```js
  const options = useMemo(() => ({ a:1 }), []);
  <Child options={options}/>
  ```

---

## ১১) Debugging tips

* Console log previous and new state:

  ```js
  setCount(prev => {
    console.log("prev:", prev);
    const next = prev + 1;
    console.log("next:", next);
    return next;
  });
  ```
* React DevTools — দেখবে component tree ও state values।
* Avoid mutating state — set breakpoints if unexpected values.

---

## ১২) Testing useState logic

* Unit test pure functions (state updaters) separately.
* For components, use React Testing Library:

  * fireEvent.click(button) and assert DOM update
  * Avoid asserting implementation detail (like setState calls), assert user-visible behavior.

---

## ১৩) Common interview / tricky questions (and answers)

1. **Q:** `setState` synchronous না asynchronous?
   **A:** Generally asynchronous in React; may be batched. functional updater safe.
2. **Q:** কেন `setState({...state})` দিয়ে reference same হলে re-render নাও হতে পারে?
   **A:** কারণ যদি object reference একই থাকে React shallow compare করে — কিন্তু setState always replaces reference; however, if you pass same reference, React may bail out (no change).
3. **Q:** `useState` vs `useRef`?
   **A:** `useState` triggers re-render when value changes; `useRef` preserves value across renders but changing `.current` does NOT trigger re-render.

---

## ১৪) Advanced patterns & tips

### A) Optimistic update pattern

* Update UI first, then call API; if API fails revert.

```js
const saveLike = async () => {
  setLiked(true); // optimistic
  try {
    await api.like(id);
  } catch {
    setLiked(false); // revert
  }
}
```

### B) Controlled form fields

* useState per input or single object for whole form — both valid.

### C) useState with TypeScript

```ts
const [count, setCount] = useState<number>(0);
const [user, setUser] = useState<{ name: string; age: number } | null>(null);
```

### D) Replace useState with useReducer when:

* আপনার state complex (nested) ও বহু অপারেশন আছে।
* আপনি চান centralized update logic (reducer) — readable and testable।

---

## ১৫) Cheatsheet: Quick patterns (copy-paste friendly)

### Set primitive

```js
setCount(5);
setFlag(prev => !prev);
```

### Increment safely

```js
setCount(prev => prev + 1);
```

### Update object field

```js
setUser(prev => ({ ...prev, age: prev.age + 1 }));
```

### Update nested object

```js
setProfile(prev => ({ ...prev, address: { ...prev.address, city: "KD" } }));
```

### Add to array

```js
setList(prev => [...prev, newItem]);
```

### Remove by index

```js
setList(prev => prev.filter((_, i) => i !== index));
```

### Replace element by index

```js
setList(prev => prev.map((v,i) => i===index ? newVal : v));
```

### Lazy init

```js
const [data, setData] = useState(() => heavyFunc());
```

---

## ১৬) ছোট ছোট practical exercises (তুমি করে দেখে বলবে — আমি রিভিউ দিব)

1. Counter with double-click that increments by 2 (use functional updater).
2. Todo list — add, remove, toggle complete (use array state).
3. Profile editor — change nested address (use nested spread).
4. useState + setInterval timer that increments sec every second (avoid stale closure).
5. Controlled form with multiple inputs — decide split vs combined state.

চাও আমি এই ৫টার জন্য ছোট কমপ্লিট কোড-ফাইল ঠিক করে দেবো যেগুলো তুমি কপি করে চালাতে পারো? প্রতিটা ফাইলের পর আমি output/explanation ও common bugs লিখে দেবো।

---

## সংক্ষিপ্ত সারমর্ম (বাংলায়)

* **সবসময় immutable** — নতুন object/array তৈরি করে আপডেট করো।
* **functional updater** (`prev => ...`) ব্যবহার করো যখন নতুন মান আগের মানের উপর নির্ভর করে।
* **nested updateে nested spread** বা `immer` ব্যবহার করো।
* **lazy initializer** ইনিশিয়ালাইজেশন expensive হলে ব্যবহার করা উচিত।
* **split vs combined** state নির্বাচন করো readability ও update-pattern অনুযায়ী।
* **stale closure** থেকে বাঁচার জন্য functional updater এবং সঠিক useEffect dependency ব্যবহার জরুরি।

---



===============================S==================================


---

# 🧩 EXERCISE 1: Counter (with double-click increment by 2)

### ✅ Code:

```jsx
import React, { useState } from "react";

export default function DoubleClickCounter() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    // ✅ functional updater, ensures correct increment each time
    setCount(prev => prev + 1);
  };

  const handleDoubleClick = () => {
    // ✅ double increment safely
    setCount(prev => prev + 2);
  };

  return (
    <div style={{ textAlign: "center", marginTop: 50 }}>
      <h2>Count: {count}</h2>
      <button onClick={handleClick} onDoubleClick={handleDoubleClick}>
        Click / Double Click
      </button>
    </div>
  );
}
```

### 🔍 Explanation:

* `useState(0)` → initializes count = 0
* `onClick` এবং `onDoubleClick` দুটি আলাদা event
* Functional updater (`prev => prev + 1`) ব্যবহার করা হয়েছে যাতে stale state সমস্যা না হয়

### ❌ If you did this instead:

```js
setCount(count + 1);
```

তাহলে যদি একাধিক event একসাথে trigger হয়, React batch করে ফেলতে পারে → মান সঠিক নাও বাড়তে পারে।

### 💡 Optimization:

Functional form সবসময় safe. Double-click করলে দুইবার render হবে (একবার per update) — এটা normal।

---

# 🧩 EXERCISE 2: Todo List (Add, Remove, Toggle Complete)

### ✅ Code:

```jsx
import React, { useState } from "react";

export default function TodoList() {
  const [todos, setTodos] = useState([
    { id: 1, text: "Learn React", done: false },
  ]);
  const [input, setInput] = useState("");

  const addTodo = () => {
    if (!input.trim()) return;
    setTodos(prev => [
      ...prev,
      { id: Date.now(), text: input, done: false }
    ]);
    setInput("");
  };

  const toggleTodo = (id) => {
    setTodos(prev =>
      prev.map(todo =>
        todo.id === id ? { ...todo, done: !todo.done } : todo
      )
    );
  };

  const removeTodo = (id) => {
    setTodos(prev => prev.filter(todo => todo.id !== id));
  };

  return (
    <div style={{ textAlign: "center", marginTop: 30 }}>
      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
        placeholder="Add task..."
      />
      <button onClick={addTodo}>Add</button>
      <ul style={{ listStyle: "none", padding: 0 }}>
        {todos.map(t => (
          <li key={t.id}>
            <span
              style={{
                textDecoration: t.done ? "line-through" : "none",
                cursor: "pointer"
              }}
              onClick={() => toggleTodo(t.id)}
            >
              {t.text}
            </span>
            <button onClick={() => removeTodo(t.id)}>❌</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### 🔍 Explanation:

* Array state ব্যবহার হচ্ছে `useState([])`।
* Add করার সময় spread `...prev` দিয়ে পুরনো todos রেখে নতুনটা যোগ করা হয়েছে।
* Toggle করার সময় map করে immutableভাবে object আপডেট।
* Remove করার সময় filter ব্যবহার করা হয়েছে।

### ❌ Wrong Example:

```js
todos.push(newTodo);
setTodos(todos); // ❌ Direct mutation — React rerender করবে না
```

### 💡 Optimization Tip:

আইটেম ID সবসময় ইউনিক দাও (যেমন Date.now()), না হলে React key collision হবে।

---

# 🧩 EXERCISE 3: Profile Editor (Nested Object Update)

### ✅ Code:

```jsx
import React, { useState } from "react";

export default function ProfileEditor() {
  const [profile, setProfile] = useState({
    name: "Md. Sakib",
    address: { city: "Dhaka", country: "Bangladesh" },
  });

  const updateCity = (newCity) => {
    setProfile(prev => ({
      ...prev,
      address: { ...prev.address, city: newCity }
    }));
  };

  return (
    <div style={{ margin: 50 }}>
      <h3>{profile.name}</h3>
      <p>City: {profile.address.city}</p>
      <input
        type="text"
        placeholder="New city..."
        onChange={(e) => updateCity(e.target.value)}
      />
    </div>
  );
}
```

### 🔍 Explanation:

* Nested object update করতে `...prev.address` করা জরুরি, না হলে direct mutation হবে।
* React shallow compare করে state update detect করে, তাই নতুন object তৈরি করতেই হবে।

### ❌ Wrong Example:

```js
profile.address.city = newCity;
setProfile(profile); // ❌ No rerender — same reference
```

### 💡 Optimization:

যদি nested structure বড় হয়, `immer` (npm i immer) ব্যবহার করা যায়:

```js
import { produce } from "immer";
setProfile(prev => produce(prev, draft => {
  draft.address.city = newCity;
}));
```

---

# 🧩 EXERCISE 4: Timer (Avoid stale closure problem)

### ✅ Code:

```jsx
import React, { useState, useEffect } from "react";

export default function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      // ✅ functional updater prevents stale closure
      setSeconds(s => s + 1);
    }, 1000);

    return () => clearInterval(id);
  }, []);

  return (
    <div style={{ textAlign: "center", marginTop: 40 }}>
      <h2>⏱ Time: {seconds}s</h2>
    </div>
  );
}
```

### 🔍 Explanation:

* `useEffect` শুধুমাত্র প্রথম রেন্ডারে চলে (empty dependency `[]`)।
* Functional updater `setSeconds(s => s + 1)` ব্যবহার করা হয়েছে কারণ otherwise callback প্রথম রেন্ডারের value ধরে রাখবে (stale closure problem)।

### ❌ Wrong Example:

```js
setSeconds(seconds + 1); // ❌ stale value, will freeze at 1
```

### 💡 Optimization:

Timer pause/resume করতে পারো: extra state `running` রেখে conditional interval set করো।

---

# 🧩 EXERCISE 5: Controlled Form (Split vs Combined state)

### ✅ Code (Combined):

```jsx
import React, { useState } from "react";

export default function SignupForm() {
  const [form, setForm] = useState({
    name: "",
    email: "",
    password: ""
  });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setForm(prev => ({ ...prev, [name]: value }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    alert(JSON.stringify(form, null, 2));
  };

  return (
    <form onSubmit={handleSubmit} style={{ margin: 50 }}>
      <input name="name" placeholder="Name" value={form.name} onChange={handleChange} />
      <input name="email" placeholder="Email" value={form.email} onChange={handleChange} />
      <input name="password" placeholder="Password" value={form.password} onChange={handleChange} />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### ✅ Alternative (Split):

```jsx
const [name, setName] = useState("");
const [email, setEmail] = useState("");
```

### 🔍 Explanation:

* Combined form makes updates dynamic using `[name]: value` pattern.
* Split form useful when fields independent (less re-render noise).

### 💡 Optimization:

If form large → useReducer better.
If small → useState fine.

---

# 🚀 Bonus: Pro-Level Tips

1. **Reset state easily**

   ```js
   setForm({ name: "", email: "", password: "" });
   ```
2. **Functional batching safe pattern**

   ```js
   setCount(c => c + 1);
   setCount(c => c + 1); // +2 correctly
   ```
3. **Avoid creating object/array inline in render**

   ```jsx
   const options = useMemo(() => ({ dark: true }), []);
   ```
4. **Keep logic outside render** → reduces re-creation cost.
5. **Prefer multiple small states** if unrelated data.

---

# 🧠 After Learning All This

তুমি এখন জানো useState সম্পর্কে:

* Initialization, update pattern
* Object/array immutability
* Functional updater advantage
* Lazy init optimization
* Nested update pitfalls
* Batching & stale closure সমস্যা
* Form handling
* Performance tips

---

