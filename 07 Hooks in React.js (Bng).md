**React.js এর সব built-in Hooks-এর পূর্ণ, step-by-step ডকুমেন্টেশন** , খুব বিস্তারিতভাবে। এখানে প্রতিটি Hook-এর জন্য থাকবে:

* কী কাজ করে
* কীভাবে কাজ করে step-by-step
* কোড উদাহরণ
* pitfalls (সাবধানতার বিষয়)
* কবে ব্যবহার করতে হয় (use case)
* class lifecycle equivalents (যদি থাকে)

---

# **React.js All Hooks — Full Documentation (Step by Step)**

---

## **1️⃣ useState**

**কাজ:** কম্পোনেন্টে লোকাল state সংরক্ষণ এবং update করা।

**Step by Step:**

1. কম্পোনেন্ট ফাংশনের ভিতরে কল করুন:

   ```jsx
   const [state, setState] = useState(initialValue);
   ```
2. React এই state কে আলাদা করে রাখে প্রতিটি কম্পোনেন্ট instance এর জন্য।
3. `setState(newValue)` কল করলে কম্পোনেন্ট পুনরায় render হয়।
4. পরবর্তী render এ `useState` সর্বশেষ value return করে।

**উদাহরণ:**

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

**Pitfalls:**

* State update asynchronous, সরাসরি পরবর্তী লাইনে পড়লে পুরোনো value পেতে পারেন।
* Array/Object mutate না করে নতুন reference return করুন।

**Use Cases:**

* Form input
* Toggle button
* Counter

**Class Equivalent:** `this.state` + `this.setState()`

---

## **2️⃣ useEffect**

**কাজ:** side-effects handle করা, যেমন API fetch, event listener, timer, DOM update।

**Step by Step:**

1. কম্পোনেন্টে কল করুন:

   ```jsx
   useEffect(() => { 
       // effect code
       return () => { /* cleanup */ };
   }, [deps]);
   ```
2. React render করার পর effect চালায়।
3. যদি dependency change হয়, আবার effect run হয়।
4. Cleanup function আগে run হয় পরবর্তী effect এর আগে।

**উদাহরণ:**

```jsx
useEffect(() => {
  const timer = setInterval(() => console.log('tick'), 1000);
  return () => clearInterval(timer);
}, []);
```

**Pitfalls:**

* Missing dependencies → stale closures
* Heavy computation → UI slow

**Use Cases:**

* Fetch API
* Event listener
* Timer
* DOM manipulation

**Class Equivalent:** `componentDidMount`, `componentDidUpdate`, `componentWillUnmount`

---

## **3️⃣ useLayoutEffect**

**কাজ:** DOM update হওয়ার পরে কিন্তু browser paint এর আগে synchronous effect চালায়।

**Use Cases:**

* DOM measurements (getBoundingClientRect)
* Synchronous style adjustments

**Example:**

```jsx
useLayoutEffect(() => {
  const rect = ref.current.getBoundingClientRect();
}, []);
```

**Pitfalls:**

* Overuse → layout thrashing

---

## **4️⃣ useRef**

**কাজ:** mutable reference ধরে রাখা, DOM access বা mutable variable store করার জন্য, re-render trigger না করে।

**Example:**

```jsx
const inputRef = useRef();
useEffect(() => inputRef.current.focus(), []);
return <input ref={inputRef} />;
```

**Use Cases:**

* DOM access
* Timer ID
* Previous value storage

**Pitfalls:**

* Overusing ref instead of state bypasses React re-render

**Class Equivalent:** `React.createRef()`

---

## **5️⃣ useContext**

**কাজ:** React context থেকে data access করা।

**Step by Step:**

1. Context তৈরি করুন:

```jsx
const ThemeContext = createContext('light');
```

2. Provider এ value set করুন:

```jsx
<ThemeContext.Provider value="dark">
  <Child />
</ThemeContext.Provider>
```

3. Consumer এ read করুন:

```jsx
const theme = useContext(ThemeContext);
```

**Use Cases:**

* Theme
* Authentication
* Language/locale

**Pitfalls:**

* Object/Array provider value → re-renders → useMemo()

**Class Equivalent:** `static contextType = ThemeContext`

---

## **6️⃣ useReducer**

**কাজ:** complex state বা multi-field state handle করা।

**Step by Step:**

```jsx
const [state, dispatch] = useReducer(reducer, initialState);

function reducer(state, action) {
  switch(action.type){
    case 'inc': return {count: state.count + 1};
    default: return state;
  }
}
dispatch({type: 'inc'});
```

**Use Cases:**

* Complex form
* Multi-step wizard
* Undo/Redo

**Class Equivalent:** complex logic inside `this.setState(prev => ...)`

---

## **7️⃣ useMemo**

**কাজ:** expensive computation cache করা, dependency change না হলে recalculation এড়ানো।

**Example:**

```jsx
const sorted = useMemo(() => expensiveSort(list), [list]);
```

**Use Cases:**

* Expensive computation
* Derived data
* Stable object references

**Pitfalls:**

* Overuse → unnecessary complexity

---

## **8️⃣ useCallback**

**কাজ:** function memoization, stable reference pass করা child props এ।

**Example:**

```jsx
const handleClick = useCallback(() => setCount(c => c + 1), []);
```

**Use Cases:**

* Child component optimization
* useEffect dependencies

---

## **9️⃣ useImperativeHandle**

**কাজ:** forwardRef ব্যবহার করে parent কে custom methods expose করা।

**Example:**

```jsx
const Input = forwardRef((props, ref) => {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current.focus()
  }));
  return <input ref={inputRef} />;
});
```

**Use Cases:**

* Custom focus/select methods

---

## **🔟 useDebugValue**

**কাজ:** Custom hook এর DevTools debugging improve করা।

```jsx
function useUser(id){
  const user = fetchUser(id);
  useDebugValue(user ? user.name : 'loading');
  return user;
}
```

---

## **1️⃣1️⃣ useTransition**

**কাজ:** non-urgent state update mark করা, UI lag কমানো।

```jsx
const [isPending, startTransition] = useTransition();
startTransition(() => setFilter(newFilter));
```

**Use Cases:**

* Large list filtering
* Expensive UI update

---

## **1️⃣2️⃣ useDeferredValue**

**কাজ:** value defer করে, responsive UI রাখে।

```jsx
const deferredQuery = useDeferredValue(query);
const results = useMemo(() => search(data, deferredQuery), [deferredQuery]);
```

---

## **1️⃣3️⃣ useId**

**কাজ:** SSR-safe unique ID generate করা।

```jsx
const id = useId();
return <label htmlFor={id}>Name</label><input id={id} />;
```

---

## **1️⃣4️⃣ useSyncExternalStore**

**কাজ:** external store subscribe করা (Redux/Zustand)।

```jsx
const state = useSyncExternalStore(store.subscribe, () => store.getState());
```

---

## **1️⃣5️⃣ useInsertionEffect**

**কাজ:** CSS-in-JS style injection খুব প্রাথমিক পর্যায়ে।

**Use Cases:**

* Rarely needed, mainly library-level

---

## **Rules of Hooks**

1. Only call hooks at the top level of React function or custom hook.
2. Only call hooks from React function or custom hook.
3. Call hooks in same order every render.

---

## **Summary Table**

| Hook                             | Primary Use                 |
| -------------------------------- | --------------------------- |
| useState                         | Local state                 |
| useEffect                        | Side-effects                |
| useLayoutEffect                  | Sync DOM updates            |
| useRef                           | DOM ref / mutable value     |
| useContext                       | Context value               |
| useReducer                       | Complex state               |
| useMemo                          | Memoized value              |
| useCallback                      | Memoized function           |
| useTransition / useDeferredValue | Concurrent UI               |
| useId                            | Unique ID                   |
| useImperativeHandle              | Custom ref methods          |
| useDebugValue                    | Custom hook debug           |
| useSyncExternalStore             | External store subscription |
| useInsertionEffect               | Style injection             |

---
