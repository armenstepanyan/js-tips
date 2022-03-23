Calculate render count using `useRef`

```
import React, { useState, useRef, useEffect } from 'react';

export default function App() {
  const [input, setInput] = useState('');
  const count = useRef(0);

  useEffect(() => {
    count.current += 1;
  });

  return (
    <div>
      <input
        type="text"
        value={input}
        onChange={(e) => setInput(e.target.value)}
      />
      <div>
        Value is <b>{input}</b>
        <p> Rendered {count.current} </p>
      </div>
    </div>
  );
}

```

If we try to use `useState` for keeping render count in state, we will get infinite loop. Because to listen all changes we must to use `useEffect` without deps and 
inside useEffect we update the state which will trigger `useEffect` again

```
// this is wrong
const [count, setCount] = useState(0);
  useEffect(() => {
    setCount(prevState => prevState + 1)
  });
```
[Example Stackblitz](https://stackblitz.com/edit/react-a-react-useref?file=src/App.js)


