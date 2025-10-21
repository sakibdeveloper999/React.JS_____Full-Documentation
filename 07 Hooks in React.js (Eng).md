
---

# **React.js All Hooks — Full Documentation (Step by Step)**

---

## **1️⃣ useState**

**Purpose:** Store and update local state in a component.

**Step by Step:**

1. Call inside a component function:

   ```jsx
   const [state, setState] = useState(initialValue);
   ```
2. React keeps this state separate for each component instance.
3. Calling `setState(newValue)` triggers a re-render.
4. On the next render, `useState` returns the latest value.

**Example:**

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

**Pitfalls:**

* State updates are asynchronous; accessing state immediately may give stale values.
* Avoid mutating arrays/objects directly—return a new reference instead.

**Use Cases:**

* Form input
* Toggle button
* Counter

**Class Equivalent:** `this.state` + `this.setState()`

---

## **2️⃣ useEffect**

**Purpose:** Handle side-effects, such as API fetching, event listeners, timers, or DOM updates.

**Step by Step:**

1. Call inside a component:

   ```jsx
   useEffect(() => { 
       // effect code
       return () => { /* cleanup */ };
   }, [deps]);
   ```
2. Runs after React renders the component.
3. If dependencies change, the effect runs again.
4. Cleanup function runs before the next effect or on unmount.

**Example:**

```jsx
useEffect(() => {
  const timer = setInterval(() => console.log('tick'), 1000);
  return () => clearInterval(timer);
}, []);
```

**Pitfalls:**

* Missing dependencies → stale closures.
* Heavy computation → UI slowdowns.

**Use Cases:**

* Fetch API data
* Add/remove event listeners
* Timers
* DOM manipulations

**Class Equivalent:** `componentDidMount`, `componentDidUpdate`, `componentWillUnmount`

---

## **3️⃣ useLayoutEffect**

**Purpose:** Run synchronous effects after DOM updates but before the browser paints.

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

* Overuse can cause layout thrashing.

---

## **4️⃣ useRef**

**Purpose:** Store a mutable reference for DOM access or mutable variables without triggering re-renders.

**Example:**

```jsx
const inputRef = useRef();
useEffect(() => inputRef.current.focus(), []);
return <input ref={inputRef} />;
```

**Use Cases:**

* DOM access
* Timer IDs
* Previous value storage

**Pitfalls:**

* Using refs instead of state bypasses React re-renders.

**Class Equivalent:** `React.createRef()`

---

## **5️⃣ useContext**

**Purpose:** Access data from a React context.

**Step by Step:**

1. Create a context:

```jsx
const ThemeContext = createContext('light');
```

2. Provide a value:

```jsx
<ThemeContext.Provider value="dark">
  <Child />
</ThemeContext.Provider>
```

3. Consume the value:

```jsx
const theme = useContext(ThemeContext);
```

**Use Cases:**

* Theme
* Authentication
* Language/locale

**Pitfalls:**

* Passing objects/arrays as context → unnecessary re-renders → use `useMemo`.

**Class Equivalent:** `static contextType = ThemeContext`

---

## **6️⃣ useReducer**

**Purpose:** Handle complex or multi-field state.

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

* Complex forms
* Multi-step wizards
* Undo/Redo functionality

**Class Equivalent:** Complex logic inside `this.setState(prev => ...)`

---

## **7️⃣ useMemo**

**Purpose:** Cache expensive computations; avoid recalculation if dependencies haven’t changed.

**Example:**

```jsx
const sorted = useMemo(() => expensiveSort(list), [list]);
```

**Use Cases:**

* Expensive computation
* Derived data
* Stable object references

**Pitfalls:**

* Overuse → unnecessary complexity.

---

## **8️⃣ useCallback**

**Purpose:** Memoize functions to pass stable references to child components.

**Example:**

```jsx
const handleClick = useCallback(() => setCount(c => c + 1), []);
```

**Use Cases:**

* Optimize child components
* Functions as `useEffect` dependencies

---

## **9️⃣ useImperativeHandle**

**Purpose:** Expose custom methods to a parent component when using `forwardRef`.

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

**Purpose:** Improve debugging for custom hooks in React DevTools.

```jsx
function useUser(id){
  const user = fetchUser(id);
  useDebugValue(user ? user.name : 'loading');
  return user;
}
```

---

## **1️⃣1️⃣ useTransition**

**Purpose:** Mark state updates as non-urgent to reduce UI lag.

```jsx
const [isPending, startTransition] = useTransition();
startTransition(() => setFilter(newFilter));
```

**Use Cases:**

* Large list filtering
* Expensive UI updates

---

## **1️⃣2️⃣ useDeferredValue**

**Purpose:** Defer value updates to keep UI responsive.

```jsx
const deferredQuery = useDeferredValue(query);
const results = useMemo(() => search(data, deferredQuery), [deferredQuery]);
```

---

## **1️⃣3️⃣ useId**

**Purpose:** Generate unique IDs safe for SSR.

```jsx
const id = useId();
return <label htmlFor={id}>Name</label><input id={id} />;
```

---

## **1️⃣4️⃣ useSyncExternalStore**

**Purpose:** Subscribe to external stores (Redux/Zustand).

```jsx
const state = useSyncExternalStore(store.subscribe, () => store.getState());
```

---

## **1️⃣5️⃣ useInsertionEffect**

**Purpose:** Inject CSS-in-JS styles at the very early stage.

**Use Cases:**

* Rarely needed, mainly library-level.

---

## **Rules of Hooks**

1. Only call hooks at the top level of React function or custom hook.
2. Only call hooks from React function or custom hook.
3. Call hooks in the same order every render.

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
