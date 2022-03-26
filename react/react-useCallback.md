```
import React, { useEffect, useState } from 'react';

export default function List({ getItems }) {
  const [items, setItems] = useState([]);

  useEffect(() => {
    setItems(getItems());
    console.log('Updating items');
  }, [getItems]);

  return items.map((item) => <div key={item}>{item}</div>);
}

```

App.js
```
import React, { useState, useCallback } from 'react';
import './style.css';
import List from './List.js';

export default function App() {
  const [number, setNumber] = useState(1);
  const [dark, setDark] = useState(false);

  const getItems = useCallback(() => {
    return [number, number + 1, number + 2];
  }, [number]);

  const theme = {
    backgroundColor: dark ? '#333' : '#FFF',
    color: dark ? '#FFF' : '#333',
  };

  return (
    <div style={theme}>
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(parseInt(e.target.value))}
      />
      <button onClick={() => setDark((prevDark) => !prevDark)}>
        Toggle theme
      </button>
      <List getItems={getItems} />
    </div>
  );
}

```

Without `useCallback`  getItems will be recalculated any time when dark value is changed. So we put it inside `useCallback` and recalculate items only if `number` is changed
[Example](https://stackblitz.com/edit/react-nrmdha?devtoolsheight=40&file=src/App.js)
