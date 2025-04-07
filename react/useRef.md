Calculate render count using `useRef`

```ts
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

```ts
// this is wrong
const [count, setCount] = useState(0);
  useEffect(() => {
    setCount(prevState => prevState + 1)
  });
```
[Example Stackblitz](https://stackblitz.com/edit/react-a-react-useref?file=src/App.js)


### Focusing input

```ts
import React, { useState, useRef } from 'react';

export default function InputEl() {
  const [input, setInput] = useState('');
  const inputRef = useRef(); // define ref

  const clickHandler = () => {
    inputRef.current.focus();
  };

  return (
    <div>
      <input
        ref={inputRef} // link ref to html element
        type="text"
        value={input}
        onChange={(e) => setInput(e.target.value)}
      />
      <button onClick={clickHandler}>Focus</button>
    </div>
  );
}

```
[Example Stackblitz](https://stackblitz.com/edit/react-a-react-useref?file=src/InputEl.js)


### ForwardRef

Ref forwarding is a technique for automatically passing a ref through a component to one of its children

Create wrapper component for input
```ts
import React from 'react';

const CustomInput = (props, ref) => {
  return <input ref={ref} {...props} />;
};

export default CustomInput;

```
Usage
```
    <div>
      <CustomInput
        ref={inputRef}
        type="text"
        value={input}
        onChange={(e) => setInput(e.target.value)}
      />
      <button onClick={clickHandler}>Focus</button>
    </div>
```
Now everything is works as before except `ref`. To pass ref to child component - CustomInput we must use `React.forwardRef`
```ts
import React from 'react';

const CustomInput = React.forwardRef((props, ref) => {
  return <input ref={ref} {...props} />;
});

export default CustomInput;
````

[Example forwardRef](https://stackblitz.com/edit/react-a-react-useref?file=src/Example3.js)


