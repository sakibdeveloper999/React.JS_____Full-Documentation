

# 📘 **Full Documentation: `useReducer` — State Management Using Reducer in React.js**

---

# 1) **WHAT is `useReducer` really? (Concept Level)**

`useReducer` is a React hook designed for predictable state transitions.

It uses a pattern called **Reducer Pattern** (popularized by Redux):

```
(state + action) => newState
```

This approach is known as a **state machine**, where:

* You have a current state
* You dispatch an action describing *what happened*
* A reducer calculates the next state (pure calculation)

**Reducer is NOT allowed to:**
❌ fetch
❌ mutate DOM
❌ use localStorage
❌ run side effects
✔️ Only compute the next state

This gives:

* Predictable updates
* Debuggable behavior
* Centralized logic
* Scalable structure
* Testable state transitions

---

# 2) **useReducer Syntax (Deep Understanding)**

```js
const [state, dispatch] = useReducer(reducerFn, initialState, initFn?)
```

* `state` → the current state snapshot
* `dispatch(action)` → triggers reducer
* `reducerFn` → pure function receiving:

  * state
  * action: `{type: string, payload?: any}`
* `initialState` → default state
* `initFn` → optional lazy initializer

---

# 3) **Difference Between `useState` vs `useReducer` (Professional POV)**

| Comparison  | useState                    | useReducer                |
| ----------- | --------------------------- | ------------------------- |
| Best for    | Simple local state          | Complex state logic       |
| State type  | Usually independent values  | Usually object/collection |
| Logic       | Scattered across components | Centralized               |
| Debugging   | Hard in large apps          | Very easy                 |
| Update type | Direct setState             | Action-driven             |
| Purpose     | Quick, simple change        | Structured transitions    |

**THUMB RULE:**

```
If you’re writing more than 3 useStates for one component, it's time for useReducer.
```

---

# 4) **Reducer Must Be PURE — Why? (Fundamental Principle)**

A reducer:

* Must return new state
* Must NOT modify existing state
* Must NOT cause side effects
* Must NOT read external mutated data

Reason:

* Predictability
* Time-travel debugging (Redux style)
* Easy testing

Pure function example:

```js
function reducer(state, action) {
  return {...state};
}
```

Impure function example (❌ bad):

```js
function reducer(state, action) {
  localStorage.setItem("data", state); // ❌ Side effect
  return state;
}
```

---

# 5) **Structure of an Action (Important)**

Minimal shape:

```js
{ type: "INCREMENT" }
```

With payload:

```js
{ type: "ADD_TODO", payload: "Learn Reducer" }
```

Advanced action shape:

```js
{
  type: "PROFILE/UPDATE_USERNAME",
  payload: { username: "sakib" },
  meta: { source: "form-submit" },
  error: false
}
```

---

# 6) **Step-by-step Lifecycle of a Reducer Update**

1️⃣ UI triggers an event
2️⃣ You call `dispatch(action)`
3️⃣ React passes current state + action to reducer
4️⃣ Reducer computes next state
5️⃣ React re-renders components that use that state

---

# 7) **Deep Explanation Example (Counter but Advanced)**

### Reducer

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

### Dispatching

```js
dispatch({ type: "INCREMENT" });
dispatch({ type: "SET", payload: 50 });
```

---

# 8) **Reducer Composition — Breaking Large Reducers**

When reducers become LARGE → break them down.

### Example Problem State

```js
{
  user: { name: "", age: 0 },
  theme: { dark: false },
  cart: { items: [], total: 0 }
}
```

Instead of:

❌ One huge reducer
✔️ Compose smaller reducers

### Good Example

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

Call it:

```js
const rootReducer = combineReducers({
  user: userReducer,
  theme: themeReducer,
  cart: cartReducer,
});
```

Then:

```js
const [state, dispatch] = useReducer(rootReducer, initialState);
```

---

# 9) **Optimizing Performance (Pro-Level)**

### Problem:

Reducers re-render all child components using that state.

### Solutions:

✔️ Memoizing child components → `React.memo`
✔️ Split state into multiple useReducers
✔️ Use `useCallback` for dispatch functions
✔️ Keep state normalized

### Normalize State Example:

Instead of nested structure:

```js
{
  todos: {
    1: {id:1, text:'Learn', completed:false}
  }
}
```

Use flat normalized structure for large apps.

---

# 10) **Lazy Initialization — Good for Heavy Startup**

Usage:

```js
function init(initial) {
  return { count: initial, timestamp: Date.now() };
}
```

Component:

```js
const [state, dispatch] = useReducer(reducer, 0, init);
```

Benefits:
✔️ Heavy initialization runs only once
✔️ Not every render

---

# 11) **Advanced Async State Management With Reducer**

Reducers cannot fetch, so do this instead:

### Pattern

```js
dispatch({ type: "FETCH_START" });

try {
  const data = await fetch(...);
  dispatch({ type: "FETCH_SUCCESS", payload: data });
} catch (err) {
  dispatch({ type: "FETCH_ERROR", payload: err });
}
```

### Reducer for async

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

# 12) **Global State Using useReducer + Context (Like Mini-Redux)**

### store.js

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

Usage:

```js
const user = useContext(StoreContext).user;
const dispatch = useContext(DispatchContext);
```

This becomes a **lightweight global state system**.

---

# 13) **Advanced: Action Creators (Cleaner Code)**

Instead of:

```js
dispatch({ type: "ADD_TODO", payload: "Learn" });
```

Make functions:

```js
const addTodo = (text) => dispatch({ type: "ADD_TODO", payload: text });
```

Benefits:
✔️ Cleaner UI
✔️ Reusable
✔️ Easy testing

---

# 14) **Advanced: immer + useReducer (Mutable Syntax, Immutable Output)**

With Immer:

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

You can mutate draft
but Immer returns pure immutable state.

---

# 15) **Middleware System (Advanced)**

You can intercept actions like Redux middleware:

```js
function loggerMiddleware(dispatch) {
  return (action) => {
    console.log("Dispatching:", action);
    return dispatch(action);
  };
}
```

Usage:

```js
const enhancedDispatch = loggerMiddleware(dispatch);
enhancedDispatch({ type: "INCREMENT" });
```

---

# 16) **Reducer Debugging Techniques**

* Log each action
* Freeze state to detect mutation
* Use React DevTools → Profiler
* Add invariant checks

Example:

```js
function reducer(state, action) {
  Object.freeze(state); // prevent mutation
  ...
}
```

---

# 17) **Unit Testing Reducers (Simple & Important)**

```js
test("ADD_TODO adds item", () => {
  const state = { todos: [] };
  const action = { type: "ADD_TODO", payload: "Learn React" };

  const newState = reducer(state, action);

  expect(newState.todos.length).toBe(1);
});
```

Reducers are **the easiest part of React to test**.

---

# 18) **Anti-Patterns (Very Important)**

❌ **Mutating state**
❌ Doing fetch inside reducer
❌ Deeply nested state
❌ Very large reducers (split them!)
❌ Using reducer when you only need useState
❌ Storing derived values in state (store raw data only)

---

# 19) **UseReducer vs Redux vs Zustand vs Jotai**

| Topic           | useReducer       | Redux      | Zustand      |
| --------------- | ---------------- | ---------- | ------------ |
| Scope           | Local or Context | Global     | Global       |
| Boilerplate     | Low              | High       | Very low     |
| Async           | Manual           | Great      | Easy         |
| DevTools        | No               | Yes        | Yes          |
| Reducer pattern | Yes              | Yes        | Optional     |
| Good for        | Moderate apps    | Large apps | Medium-large |

---

# 20) **Real-World Example: Shopping Cart System**

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
* APPLY_COUPON
* CLEAR_CART
* UPDATE_QTY
* CALCULATE_TOTAL

Reducer handles all business logic.

---

# 21) **Real-World Example: Form State Manager**

Better than multiple useStates:

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

Reducer processes:

* CHANGE_FIELD
* VALIDATE_FIELD
* SUBMIT
* SUBMIT_SUCCESS
* SUBMIT_ERROR

This becomes a small state machine.

---

# 22) **Summary (Professional Level)**

`useReducer` is best when:

* Complex state
* Many actions
* Predictable transitions
* Centralized update rules
* Multiple dispatch locations
* Testable pure logic
* Large components
* Intermediate global state (via context)

---
