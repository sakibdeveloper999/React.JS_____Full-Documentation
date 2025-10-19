
It includes:

* 🌟 How to **create and use a function** in React (with real-life example)
* 📋 Full **event handler table** (with descriptions and example usage)
* 💡 Clean Markdown styling ready for upload

---

# ⚛️ React.js Built-in Functions (Event Handlers) — Complete Guide

React.js provides many built-in **event handler functions** (like `onClick`, `onChange`, `onSubmit`, etc.) that allow you to handle user interactions easily and cleanly inside components.

---

## 🧠 What is an Event Handler in React?

An **event handler** in React is a function that gets triggered when a specific event occurs — such as a click, typing, hover, or form submission.

React events are written in **camelCase** (like `onClick`) and passed as **functions**, not strings.

---

## 🪄 How to Create and Use a Function in React.js

Let’s see a **real-life example** 👇

### Example: Button Click Counter

```jsx
import React, { useState } from "react";

function CounterApp() {
  // Step 1: Create a state variable
  const [count, setCount] = useState(0);

  // Step 2: Create a function to handle click
  const handleClick = () => {
    setCount(count + 1);
  };

  // Step 3: Use the function inside JSX with onClick
  return (
    <div style={{ textAlign: "center", marginTop: "40px" }}>
      <h1>Clicked {count} times</h1>
      <button onClick={handleClick}>Click Me 👆</button>
    </div>
  );
}

export default CounterApp;
```

✅ **Explanation:**

1. `useState` manages the count state.
2. `handleClick` function updates the state when the button is clicked.
3. `onClick` calls `handleClick` each time you click the button.

💡 Real-life Use Case:
This pattern is used in dashboards (like increasing cart items), blog likes, quiz answers, voting buttons, and anywhere user interaction is needed.

---

## 🧾 Full React Built-in Event Handlers Cheat Sheet

Below is a full categorized list of all built-in event handlers with descriptions and example usage.

---

### 🖱️ Mouse Events

| Event           | Description                             | Example Usage                |
| --------------- | --------------------------------------- | ---------------------------- |
| `onClick`       | Fires when an element is clicked        | `<button onClick={...}>`     |
| `onContextMenu` | Fires on right-click                    | `<div onContextMenu={...}>`  |
| `onDoubleClick` | Fires when an element is double-clicked | `<div onDoubleClick={...}>`  |
| `onMouseDown`   | When mouse button is pressed            | `<button onMouseDown={...}>` |
| `onMouseUp`     | When mouse button is released           | `<button onMouseUp={...}>`   |
| `onMouseEnter`  | Mouse enters an element                 | `<div onMouseEnter={...}>`   |
| `onMouseLeave`  | Mouse leaves an element                 | `<div onMouseLeave={...}>`   |
| `onMouseMove`   | Fires when mouse moves                  | `<div onMouseMove={...}>`    |
| `onMouseOver`   | Similar to `onMouseEnter` but bubbles   | `<div onMouseOver={...}>`    |
| `onMouseOut`    | Similar to `onMouseLeave` but bubbles   | `<div onMouseOut={...}>`     |

---

### ⌨️ Keyboard Events

| Event        | Description                        | Example Usage              |
| ------------ | ---------------------------------- | -------------------------- |
| `onKeyDown`  | When a key is pressed              | `<input onKeyDown={...}>`  |
| `onKeyPress` | When a key is pressed (deprecated) | `<input onKeyPress={...}>` |
| `onKeyUp`    | When a key is released             | `<input onKeyUp={...}>`    |

---

### 🧾 Form Events

| Event       | Description              | Example Usage              |
| ----------- | ------------------------ | -------------------------- |
| `onChange`  | Detects value change     | `<input onChange={...}>`   |
| `onInput`   | Fires on every keystroke | `<textarea onInput={...}>` |
| `onSubmit`  | When form is submitted   | `<form onSubmit={...}>`    |
| `onReset`   | When form is reset       | `<form onReset={...}>`     |
| `onInvalid` | Input validation fails   | `<input onInvalid={...}>`  |

---

### 🎯 Focus Events

| Event     | Description         | Example Usage           |
| --------- | ------------------- | ----------------------- |
| `onFocus` | Element gains focus | `<input onFocus={...}>` |
| `onBlur`  | Element loses focus | `<input onBlur={...}>`  |

---

### 📋 Clipboard Events

| Event     | Description               | Example Usage           |
| --------- | ------------------------- | ----------------------- |
| `onCopy`  | Fires when text is copied | `<input onCopy={...}>`  |
| `onCut`   | Fires when text is cut    | `<input onCut={...}>`   |
| `onPaste` | Fires when text is pasted | `<input onPaste={...}>` |

---

### 🧩 Drag & Drop Events

| Event         | Description           | Example Usage             |
| ------------- | --------------------- | ------------------------- |
| `onDrag`      | While dragging        | `<div onDrag={...}>`      |
| `onDragStart` | Drag starts           | `<div onDragStart={...}>` |
| `onDragEnd`   | Drag ends             | `<div onDragEnd={...}>`   |
| `onDragEnter` | Item enters drop zone | `<div onDragEnter={...}>` |
| `onDragLeave` | Item leaves drop zone | `<div onDragLeave={...}>` |
| `onDragOver`  | While over drop zone  | `<div onDragOver={...}>`  |
| `onDrop`      | Item dropped          | `<div onDrop={...}>`      |

---

### 📱 Touch Events

| Event           | Description           | Example Usage               |
| --------------- | --------------------- | --------------------------- |
| `onTouchStart`  | Finger touches screen | `<div onTouchStart={...}>`  |
| `onTouchMove`   | Finger moves          | `<div onTouchMove={...}>`   |
| `onTouchEnd`    | Finger leaves screen  | `<div onTouchEnd={...}>`    |
| `onTouchCancel` | Touch interrupted     | `<div onTouchCancel={...}>` |

---

### 🖐️ Pointer Events

| Event             | Description      | Example Usage                 |
| ----------------- | ---------------- | ----------------------------- |
| `onPointerDown`   | Pointer pressed  | `<div onPointerDown={...}>`   |
| `onPointerMove`   | Pointer moves    | `<div onPointerMove={...}>`   |
| `onPointerUp`     | Pointer released | `<div onPointerUp={...}>`     |
| `onPointerEnter`  | Pointer enters   | `<div onPointerEnter={...}>`  |
| `onPointerLeave`  | Pointer leaves   | `<div onPointerLeave={...}>`  |
| `onPointerOver`   | Pointer over     | `<div onPointerOver={...}>`   |
| `onPointerOut`    | Pointer out      | `<div onPointerOut={...}>`    |
| `onPointerCancel` | Pointer canceled | `<div onPointerCancel={...}>` |

---

### 🧭 Scroll & Wheel Events

| Event      | Description        | Example Usage          |
| ---------- | ------------------ | ---------------------- |
| `onScroll` | Element scrolled   | `<div onScroll={...}>` |
| `onWheel`  | Mouse wheel scroll | `<div onWheel={...}>`  |

---

### 🎧 Media Events

| Event            | Description           | Example Usage                  |
| ---------------- | --------------------- | ------------------------------ |
| `onPlay`         | Media starts playing  | `<video onPlay={...}>`         |
| `onPause`        | Media paused          | `<video onPause={...}>`        |
| `onEnded`        | Media playback ends   | `<video onEnded={...}>`        |
| `onTimeUpdate`   | Playback time updates | `<video onTimeUpdate={...}>`   |
| `onVolumeChange` | Volume changed        | `<video onVolumeChange={...}>` |
| `onError`        | Media error           | `<video onError={...}>`        |
| `onLoadedData`   | Media data loaded     | `<video onLoadedData={...}>`   |
| `onCanPlay`      | Media can play        | `<video onCanPlay={...}>`      |
| `onWaiting`      | Waiting for buffer    | `<video onWaiting={...}>`      |

---

### 💫 Animation Events

| Event                  | Description       | Example Usage                      |
| ---------------------- | ----------------- | ---------------------------------- |
| `onAnimationStart`     | Animation starts  | `<div onAnimationStart={...}>`     |
| `onAnimationEnd`       | Animation ends    | `<div onAnimationEnd={...}>`       |
| `onAnimationIteration` | Animation repeats | `<div onAnimationIteration={...}>` |

---

### 🌈 Transition Events

| Event             | Description             | Example Usage                 |
| ----------------- | ----------------------- | ----------------------------- |
| `onTransitionEnd` | CSS transition finished | `<div onTransitionEnd={...}>` |

---

### 🈹 Composition Events

| Event                 | Description         | Example Usage                       |
| --------------------- | ------------------- | ----------------------------------- |
| `onCompositionStart`  | Composition starts  | `<input onCompositionStart={...}>`  |
| `onCompositionUpdate` | Composition updates | `<input onCompositionUpdate={...}>` |
| `onCompositionEnd`    | Composition ends    | `<input onCompositionEnd={...}>`    |

---

### 🧠 Miscellaneous Events

| Event      | Description            | Example Usage            |
| ---------- | ---------------------- | ------------------------ |
| `onSelect` | Text selected          | `<input onSelect={...}>` |
| `onLoad`   | Resource loaded        | `<img onLoad={...}>`     |
| `onError`  | Error loading resource | `<img onError={...}>`    |

---

## 🧩 Summary

React event handlers are categorized into:

* 🖱️ Mouse Events
* ⌨️ Keyboard Events
* 🧾 Form Events
* 🎯 Focus Events
* 📋 Clipboard Events
* 🧩 Drag & Drop Events
* 📱 Touch & Pointer Events
* 🎧 Media Events
* 💫 Animation / Transition Events
* 🈹 Composition Events
* 🧭 Scroll / Wheel / Miscellaneous

---

## 🚀 Final Words

* React event handlers are **synthetic** — they work consistently across browsers.
* Always use **camelCase** (e.g., `onClick`, not `onclick`).
* Pass **function references**, not calls → `onClick={handleClick}` ✅ not `onClick={handleClick()}` ❌

---
