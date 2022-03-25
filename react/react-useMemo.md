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

