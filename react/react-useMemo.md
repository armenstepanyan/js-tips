```
import React, { useState } from 'react'

export default function MemoComp() {

    const [number, setNumber] = useState(0);
    const [dark, setDark] = useState(false);
    const doubleNumber = slowFunction(number);
    const themeStyles = {
        backgroundColor: dark ? 'black' : 'white',
        color: dark ? 'white' : 'black'
    }

    return (
        <>
          <input type="text" value={number} onChange={e => {
              setNumber(parseInt(e.target.value))
          }}/>  
          <button onClick={() => setDark(prevDark => !prevDark)}>Change theme</button>
          <div style={themeStyles}>{doubleNumber}</div>
        </>
    )
}

function slowFunction(number) {
    console.log('calculate');
    for(let i = 0; i < 1000000000; i++) {}
    return 2 * number
}
```

The `doubleNumber` function will be recalculated each time when number or theme variable changes. Here we can use `useMemo` hook

```
import React, { useState, useMemo } from 'react'
----
const doubleNumber = useMemo(() => {
    return slowFunction(number)
}, [number])
```

If we add `useEffect` and listen `themeStyles` changes, it also will be triggered when number value is changed because on every render  `themeStyles` get new reference
```
useEffect(() => {
    console.log('theme changed')
}, [themeStyles])
```

To avoid this we can use memo also for themeStyles
```
const themeStyles = useMemo(() => {
    return {
        backgroundColor: dark ? 'black' : 'white',
        color: dark ? 'white' : 'black'
    }
}, [dark])
```

Whole code
```
import React, { useState, useMemo, useEffect } from 'react'

export default function MemoComp() {

    const [number, setNumber] = useState(0);
    const [dark, setDark] = useState(false);
    // calculate only if number is changed
    const doubleNumber = useMemo(() => {
        return slowFunction(number)
    }, [number])

    const themeStyles = useMemo(() => {
        return {
            backgroundColor: dark ? 'black' : 'white',
            color: dark ? 'white' : 'black'
        }
    }, [dark])

    useEffect(() => {
        console.log('theme changed')
    }, [themeStyles])

    return (
        <>
          <input type="number" value={number} onChange={e => {
              setNumber(parseInt(e.target.value))
          }}/>  
          <button onClick={() => setDark(prevDark => !prevDark)}>Change theme</button>
          <div style={themeStyles}>{doubleNumber}</div>
        </>
    )
}

function slowFunction(number) {
    console.log('calculate');
    for(let i = 0; i < 100000000; i++) {}
    return 2 * number
}
```
[Stackblitz](https://stackblitz.com/edit/react-aag4db?devtoolsheight=33&file=src/MemoComp.js)

### Example 2
This component will be rendered each time when state is changed. And each time when we click on button to change style.background `const len = getTextLength(name)` will called again. We need to call `getTextLength` function only if `name` is changed.  
```
import React, { useState } from 'react';

function getTextLength(s: string) {
  console.log('func called');
  return s.length;
}

export default function Form() {
  console.log('component rendered');
  const [name, setName] = useState('');
  const [isGreen, setIsGreen] = useState(false);
  const len = getTextLength(name); // <-- called each time when state is changed

  return (
    <div>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <p style={{ backgroundColor: !isGreen ? 'green' : 'blue' }}>{name}</p>
      <p>Text length is {len}</p>
      <button onClick={() => setIsGreen((prev) => !prev)}>Change color</button>
    </div>
  );
}
```

Using useMemo
```
export default function Form() {
  console.log('component rendered');
  const [name, setName] = useState('');
  const [isGreen, setIsGreen] = useState(false);
  const len = useMemo(() => {
    return getTextLength(name)
  }, [name]) // <-- function will be called only if name is changed

  return (
    <div>
      ...
    </div>
  );
}
```

[Example](https://stackblitz.com/edit/a-react-usememo2?file=Form.tsx)
