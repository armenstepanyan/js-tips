### useState

React’s useState hook is one of the most commonly used features when building functional components. It allows you to store and manage local component state.

✅ Basic Syntax
```ts
const [state, setState] = useState(initialValue);

```

- `state`: The current value of the state.
- `setState`: A function to update the state.
- `initialValue`: The value used to initialize the state when the component is first rendered.

📘 Example: Simple State
```ts
import React, { useState } from 'react';

function ExampleComponent() {
  const [isVisible, setIsVisible] = useState(false);

  return (
    <button onClick={() => setIsVisible(!isVisible)}>
      {isVisible ? 'Hide' : 'Show'}
    </button>
  );
}

```
In this case, the component state is initialized to false, and setIsVisible is used to toggle it.

### 🐢 Lazy Initialization with Arrow Function
For simple values like booleans, strings, or numbers, it's fine to pass the initial value directly.

But if the initial value is the result of an expensive computation, you should use a lazy initializer function:

```ts
const [state, setState] = useState(() => expensiveComputation());

```

This ensures the function runs only once during the initial render, not on every re-render.

#### 🛠 Example: When to Use Arrow Function in useState

```ts
function computeInitialValue() {
  console.log('Expensive computation running...');
  // Simulate expensive logic
  return 42;
}

function LazyInitComponent() {
  const [value, setValue] = useState(() => computeInitialValue());

  return (
    <div>
      <p>Value: {value}</p>
      <button onClick={() => setValue(value + 1)}>Increment</button>
    </div>
  );
}

```

