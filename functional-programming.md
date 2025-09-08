### 🧠 Functional vs. Non-Functional Programming in JavaScript: A Developer’s Guide
When preparing for JavaScript interviews, one of the concepts that often trips up even experienced developers is understanding functional programming (FP) versus non-functional (typically imperative or object-oriented) programming. These paradigms reflect different philosophies on how to write, organize, and reason about code.

This article breaks down both paradigms with definitions, examples, use cases, and a pros/cons comparison — helping you sharpen your thinking and ace your interviews.

#### 📌 What Is Functional Programming?
**Functional Programming** is a programming paradigm where computation is treated as the evaluation of mathematical functions. It emphasizes pure functions, immutability, and statelessness.

✅ Key Characteristics

- Pure functions – No side effects, given the same input always return the same output.

- Immutability – Data is never modified; instead, new data is created.

- First-class functions – Functions are treated as values (can be passed, returned, and stored).

- Higher-order functions – Functions that take other functions as arguments or return them.

#### 🧪 Functional Example
```ts
// Pure function
const add = (a, b) => a + b;

// Higher-order function
const mapArray = (arr, fn) => {
  return arr.map(fn);
};

const numbers = [1, 2, 3];
const squared = mapArray(numbers, num => num * num);

console.log(squared); // [1, 4, 9]

```

#### 🚫 What Is Non-Functional Programming?
**Non-Functional Programming** generally refers to imperative or object-oriented approaches, where you write instructions that change the program’s state.

✅ Key Characteristics

- Uses mutable state (e.g., variables that change).

- Code is written as a sequence of instructions.

- Objects are used to encapsulate state and behavior.

```ts
let numbers = [1, 2, 3];
let squared = [];

for (let i = 0; i < numbers.length; i++) {
  squared.push(numbers[i] * numbers[i]);
}

console.log(squared); // [1, 4, 9]

```

#### 📦 When to Use Functional Programming?

Functional style shines in:

- Data transformations (e.g., using .map(), .filter(), .reduce()).
- UI rendering (React's core principles are FP-based).
- Concurrent or async environments (e.g., serverless, microservices).
- Unit testing-heavy projects (pure functions = easy to test).

####  🏗️ When Non-Functional (Imperative/OOP) Is Better?

Imperative or object-oriented style is useful when:
- Managing complex state (e.g., games, simulations).
- You need explicit control flow (e.g., low-level performance optimization).
- You're working on legacy codebases or with certain frameworks.
- You want to encapsulate behavior with state (OOP is often better here).

#### 🧠 Example Interview Question:
Refactor the following code using functional programming principles.
```ts
let result = [];
for (let i = 0; i < arr.length; i++) {
  if (arr[i] % 2 === 0) {
    result.push(arr[i] * 2);
  }
}
```

✅ Functional Refactor:
```ts
const result = arr
  .filter(num => num % 2 === 0)
  .map(num => num * 2);

```

### ⚛️ React — Functional by Design
React has embraced functional programming more deeply over time, especially with the introduction of Hooks in React 16.8. Today, React encourages a functional approach.

🔑 Key Functional Aspects in React

- Function components instead of class components
- Hooks (useState, useEffect, etc.) to manage state and side effects
- Emphasis on pure components (deterministic UI rendering)
- Uses immutable state updates

✅ Example: Functional React Component

```ts
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  const increment = () => setCount(prev => prev + 1);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>Increment</button>
    </div>
  );
}

```

💡 What Makes It Functional?

- Counter is a pure function from props/state → UI.
- useState handles state immutably.
- Side effects (e.g., useEffect) are isolated and declarative.

### 🅰️ Angular — More Object-Oriented / Imperative
Angular, on the other hand, leans more toward object-oriented and imperative patterns. It uses classes, decorators, and dependency injection — features that reflect a non-functional style.

🔑 Key Non-Functional Aspects in Angular

- Uses class-based components 
- Emphasis on services and DI (Dependency Injection)
- State is often mutable and imperative
- Templates contain embedded logic, not pure functions

✅ Example: Angular Component (Non-Functional)
```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-counter',
  template: `
    <p>Count: {{ count }}</p>
    <button (click)="increment()">Increment</button>
  `
})
export class CounterComponent {
  count = 0;

  increment() {
    this.count++;
  }
}

```
💡 What Makes It Non-Functional?

- The component is a class with mutable state.
- `increment()` directly changes this.count.
- Angular uses imperative DOM interaction in some cases (though it’s still declarative via templates).

#### 🔄 Can You Use Functional Programming in Angular?

Yes — Angular supports `RxJS`, which is a functional reactive programming library. It allows you to compose complex async flows using pure functions.
But overall, Angular’s core architecture is still more OOP-driven than React’s.
