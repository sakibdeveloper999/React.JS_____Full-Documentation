
# 📘 **useReducer — State Management Using Reducer (Full Bangla Documentation)**

(React.js — Beginner to Advanced)

---

# ১) **useReducer কী? (মূল ধারণা)**

`useReducer` হলো React-এর একটি Hook, যেটা **predictable state management** করার জন্য ব্যবহৃত হয়।

এটি একটি Reducer Pattern অনুসরণ করে:

```
(state + action) => newState
```

এর মানে:

* আপনার কাছে একটি current state আছে
* আপনি একটি action dispatch করেন (যা বলে কী ঘটলো)
* reducer নতুন state রিটার্ন করে

Reducer হলো একদম pure function —
❌ এতে fetch করা যাবে না
❌ reducer state mutate করতে পারবে না
❌ DOM আপডেট করতে পারবে না
❌ localStorage ব্যবহার করতে পারবে না
✔️ শুধু current state → next state ক্যালকুলেট করবে

এভাবে state predictable থাকে, debug করা সহজ হয় এবং structure পরিষ্কার থাকে।

---

# ২) **useReducer Syntax — গভীরভাবে বোঝা**

```js
const [state, dispatch] = useReducer(reducerFn, initialState, initFn?)
```

* **state** → বর্তমান state
* **dispatch(action)** → state আপডেট ট্রিগার করে
* **reducerFn** → একটি pure function
* **initialState** → default state
* **initFn** → (ঐচ্ছিক) lazy initialization

---

# ৩) **useState বনাম useReducer (পেশাদারি দৃষ্টিভঙ্গি)**

| তুলনা    | useState               | useReducer                 |
| -------- | ---------------------- | -------------------------- |
| ব্যবহার  | ছোট state              | জটিল বা structured state   |
| State    | আলাদা আলাদা            | সাধারণত অবজেক্ট/collection |
| Logic    | কম্পোনেন্টে ছড়িয়ে থাকে | কেন্দ্রীভূত থাকে           |
| Debug    | কঠিন                   | সহজ                        |
| Update   | সরাসরি setState        | Action-driven              |
| Best for | Simple UI              | Complex Business Logic     |

**Golden Rule:**

```
একটি কম্পোনেন্টে ৩টির বেশি useState ব্যবহার করলে useReducer ভাবুন।
```

---

# ৪) **Reducer PURE হওয়া বাধ্যতামূলক — কেন?**

কারণ reducer:

* predictable রাখতে হবে
* testable রাখতে হবে
* debugging সহজ রাখতে হবে

Reducer কখনই side effect করবে না।
শুধু state → newState রিটার্ন করবে।

---

# ৫) **Action Object — ভিতরের গঠন**

সাধারণত এই রকম:

```js
{ type: "INCREMENT" }
```

Payload সহ:

```js
{ type: "ADD_TODO", payload: "Learn Reducer" }
```

---

# ৬) **Reducer Update Lifecycle (step-by-step)**

1️⃣ ইউজার কিছু করে (ক্লিক / টাইপ)
2️⃣ dispatch(action) কল হয়
3️⃣ React → reducer(state, action) চালায়
4️⃣ reducer নতুন state দেয়
5️⃣ React re-render করে

---

# ৭) **উন্নত লেভেলের Counter Example**

### reducer

```js
function counterReducer(state, action) {
  switch (action.type) {
    case "INCREMENT": 
      return { count: state.count + 1 };

    case "DECREMENT":
      return { count: state.count - 1 };

    case "SET":
      return { count: action.payload };

    default:
      return state;
  }
}
```

### Component

```js
const [state, dispatch] = useReducer(counterReducer, { count: 0 });
```

### Dispatch

```js
dispatch({ type: "INCREMENT" });
dispatch({ type: "SET", payload: 50 });
```

---

# ৮) **Reducer Composition — বড় Reducer ভাঙা**

একটা বিশাল reducer → কোড মেইনটেইন করা কঠিন।

Better:

* state আলাদা আলাদা অংশে ভাগ করা
* প্রতিটা অংশের আলাদা reducer রাখা

### combineReducers (Redux এর মতো)

```js
function combineReducers(reducers) {
  return function (state, action) {
    const newState = {};
    for (let key in reducers) {
      newState[key] = reducers[key](state[key], action);
    }
    return newState;
  };
}
```

---

# ৯) **Performance Optimization**

✔️ React.memo
✔️ useCallback
✔️ Nested state avoid
✔️ Multiple useReducer splitting
✔️ State normalization ব্যবহার করা

---

# ১০) **Lazy Initialization — heavy কাজ একবার করার কৌশল**

```js
function init(initial) {
  return { count: initial, timestamp: Date.now() };
}
```

```js
const [state, dispatch] = useReducer(reducer, 0, init);
```

এটি initial setup দ্রুত করে।

---

# ১১) **Async State Handling (ব্যবহারিক উদাহরণ)**

Reducer async করতে পারে না। তাই async UI থেকে করতে হবে।

### Flow:

```js
dispatch({ type: "FETCH_START" });

try {
  const data = await fetch(...);
  dispatch({ type: "FETCH_SUCCESS", payload: data });
} catch (err) {
  dispatch({ type: "FETCH_ERROR", payload: err });
}
```

### Reducer

```js
function reducer(state, action) {
  switch(action.type) {
    case "FETCH_START":
      return {...state, loading: true};

    case "FETCH_SUCCESS":
      return { data: action.payload, loading: false, error: null };

    case "FETCH_ERROR":
      return { ...state, loading: false, error: action.payload };

    default:
      return state;
  }
}
```

---

# ১২) **useReducer + useContext → একটি Mini Redux Architecture**

### Context Store

```js
export const StoreContext = createContext();
export const DispatchContext = createContext();

export function StoreProvider({ children }) {
  const [state, dispatch] = useReducer(rootReducer, initialState);

  return (
    <StoreContext.Provider value={state}>
      <DispatchContext.Provider value={dispatch}>
        {children}
      </DispatchContext.Provider>
    </StoreContext.Provider>
  );
}
```

এটি global state তৈরি করে।

---

# ১৩) **Action Creators**

UI থেকে সরাসরি dispatch লেখা খারাপ।
Function বানিয়ে পাঠানো ভালো:

```js
const addTodo = (text) => dispatch({ type: "ADD_TODO", payload: text });
```

---

# ১৪) **Immer + useReducer — Mutable কোড, Immutable output**

```js
import produce from "immer";

const reducer = produce((draft, action) => {
  switch(action.type){
    case "ADD":
      draft.todos.push(action.payload);
      break;
  }
});
```

---

# ১৫) **Reducer Middleware (Advanced Concept)**

```js
function loggerMiddleware(dispatch) {
  return (action) => {
    console.log("Dispatching:", action);
    return dispatch(action);
  };
}
```

---

# ১৬) **Debugging Reducer**

✔️ action log
✔️ state freeze
✔️ profiler
✔️ invariant checks

---

# ১৭) **Reducer Unit Testing**

```js
test("ADD_TODO adds item", () => {
  const state = { todos: [] };
  const action = { type: "ADD_TODO", payload: "Learn React" };

  const newState = reducer(state, action);

  expect(newState.todos.length).toBe(1);
});
```

---

# ১৮) **Anti-Patterns — যা করা যাবে না**

❌ Reducer-এ API call
❌ Reducer-এ side effect
❌ State mutate করা
❌ বড় Reducer একটাই রাখা
❌ useReducer যেখানে দরকার নয়
❌ Derived state রাখা

---

# ১৯) **useReducer vs Redux vs Zustand**

|             | useReducer             | Redux            | Zustand                  |
| ----------- | ---------------------- | ---------------- | ------------------------ |
| Scope       | Local + Context        | Global App       | Global App               |
| Boilerplate | কম                     | বেশি             | খুব কম                   |
| Async       | manual                 | best             | easy                     |
| DevTools    | না                     | আছে              | আছে                      |
| Best        | Structured local state | Large-scale apps | Lightweight global state |

---

# ২০) **Real World Example — Shopping Cart**

State:

```js
{
  items: [],
  total: 0,
  coupon: null,
}
```

Actions:

* ADD_ITEM
* REMOVE_ITEM
* UPDATE_QUANTITY
* APPLY_COUPON
* CALCULATE_TOTAL
* CLEAR_CART

Business logic reducer-এ centralized থাকে।

---

# ২১) **Real World Example — Form Manager**

```js
{
  name: "",
  email: "",
  password: "",
  touched: {},
  errors: {},
  status: "idle"
}
```

Actions:

* CHANGE_FIELD
* VALIDATE
* SUBMIT
* SUCCESS
* FAIL

এটা একটি ছোট state machine এর মতো হয়।

---

# ২২) **Conclusion (সংক্ষেপে)**

useReducer ব্যবহার করুন যখন:

* state জটিল
* অনেক action লাগে
* central logic দরকার
* component বড়
* predictable transitions দরকার
* testable business logic দরকার

---
