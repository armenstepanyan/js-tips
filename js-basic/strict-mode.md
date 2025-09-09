### Strict Mode in JavaScript

JavaScript's strict mode is a way to opt in to a restricted version of the language. It helps catch common coding mistakes and prevents certain unsafe actions.

You enable it by adding:

```ts
'use strict';
```
at the beginning of a script or a function.

#### Why Use Strict Mode?
Strict mode:

- Eliminates some silent errors by converting them into **explicit errors**
- Fixes mistakes that make JavaScript engines **harder to optimize.**
- Prohibits certain syntax likely to be defined in future versions of ECMAScript
- Changes how the **this** keyword behaves in functions.

#### 📌 this Behavior Comparison
Here's how the this keyword behaves with and without strict mode in function calls.

🔓 Without Strict Mode (Default in Non-Module Scripts)
```ts
function f1(){
  console.log(this)
}

f1();   // Window
f1.call(null);    // Window
f1.call(undefined);   // Window
f1.call('');    // String {''}
f1.call(true);    // Boolean {true}
f1.call(1);   // Number {1}
f1.call({});    // {}
```

- When `f1()` is called **without a context**, this defaults to the **global object** (window in browsers).
- If you pass a **primitive value** like true, 1, or '', JavaScript **boxes** them into object wrappers.

🔐 With Strict Mode
```ts
function f1(){
  'use strict'; 
  console.log(this)
}

f1();     //undefined
f1.call(null);    // null
f1.call(undefined);   // undefined
f1.call('');    // ''
f1.call(true);    // true
f1.call(1);     // 1
f1.call({});     // {}
```
- In strict mode, this **remains exactly what it's set to** —JavaScript doesn't coerce it.
- If no context is provided (e.g., f1()), this is undefined.
- No boxing of primitives. this stays as the primitive (true, '', etc.).

| Scenario             | Non-Strict Mode (`this`) | Strict Mode (`this`) |
| -------------------- | ------------------------ | -------------------- |
| `f1()`               | `window`                 | `undefined`          |
| `f1.call(null)`      | `window`                 | `null`               |
| `f1.call(undefined)` | `window`                 | `undefined`          |
| `f1.call('')`        | `String {""}`            | `""` (primitive)     |
| `f1.call(true)`      | `Boolean {true}`         | `true` (primitive)   |
| `f1.call(1)`         | `Number {1}`             | `1` (primitive)      |
| `f1.call({})`        | `{}`                     | `{}`                 |
