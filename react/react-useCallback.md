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

Let’s say we have a component that given a userId displays the user’s data.
```
import React, { useEffect, useState } from 'react';

function User({ userId }) {
  const [user, setUser] = useState({ name: '', email: '' });

  const fetchUser = async () => {
    console.log('called');
    const res = await fetch(
      `https://jsonplaceholder.typicode.com/users/${userId}`
    );
    const newUser = await res.json();
    setUser(newUser);
  };

  useEffect(() => {
    fetchUser();
  }, []); // <---- eslint error, need to add dependency
  
  return (
    <div>
      <p>
        User: <b>{user?.name}</b>
      </p>
    </div>
  );
}

export default User;
```
If we have `eslint-plugin-react-hooks` installed, it will yell at you for omitting fetchUser from `useEffect`’s dependencies
```
const fetchUser = async () => {
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/users/${userId}`
  );
  const newUser = await res.json();
  setUser(newUser); // 🔴 setState triggers re-render
};

useEffect(() => {
  fetchUser();
}, [fetchUser]); // fetchUser is a new function on every render
```
There are two ways to solve this issue. Admittedly the most elegant would be to move `fetchUser` inside `useEffect` and add `userId` as a dependency
```
// 1. Way to solve the infinite loop
useEffect(() => {
  const fetchUser = async () => {
    const res = await fetch(
      `https://jsonplaceholder.typicode.com/users/${userId}`
    );
    const newUser = await res.json();
    setUser(newUser); // Triggers re-render, but only if userId was changed
  };

  fetchUser();
}, [userId]); // ✅ ... userId stays the same.
```
Second way is usng `useCallback`
```
// 2. Way to solve the infinite loop
const fetchUser = useCallback(async () => {
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/users/${userId}`
  );
  const newUser = await res.json();
  setUser(newUser);
}, [userId]);

useEffect(() => {
  fetchUser();
}, [fetchUser]); // ✅ fetchUser stays the same between renders
```
### Summary
- With `useCallback` you can define a **function** that has referential equality between renders.
- You can use `useMemo` to calculate a **value** that has referential equality between renders.

[Example](https://stackblitz.com/edit/a-react-ts-usecallback?file=User.tsx)
