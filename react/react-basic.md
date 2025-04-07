### Virtual DOM
Virtual DOM is a “virtual” representation of a UI which is kept 
in memory and synced with the “real” DOM by a library such as ReactDOM.
This process is called reconciliation. **Reconciliation** is the process through which React updates the Browser DOM.

Important concepts behind the working of the Reconciliation process are:

- Virtual DOM  
- Diffing Algorithm

React renders JSX components to the Browser DOM, but keeps a copy of the actual DOM to itself.
The following actions take place in React:

- React stores a copy of Browser DOM which is called Virtual DOM.
- When we make changes or add data, React creates a new Virtual DOM and compares it with the previous one
- Comparison is done by **Diffing Algorithm**. Note:  These comparisons take place in the memory and nothing is yet changed in the Browser
- After comparing, React goes ahead and creates a new Virtual DOM having the changes
- Then it updates the Browser DOM with the least number of changes possible without rendering the entire DOM again

### How does this Virtual DOM compare itself to its previous version?
This is where the **Diffing Algorithm** comes into play. When diffing two trees, React first compares the two root elements. 
The behavior is different depending on the types of the root elements. Some concepts used by this Algorithm are:

- Two elements of different types will produce different trees.
- **Breadth-First Search** (BFS) is applied because if a node is found as changed, it will re-render the entire subtree hence Depth First Approach is not exactly optimal.
- When comparing two elements of the same type, keep the underlying node as same and only update changes in **attributes** or **styles**.
- React uses optimizations so that a minimal difference can be calculated in O(N) efficiently using this Algorithm


### Depth-first
With DFS of non-tree graphs, just like with trees, we follow a single line of child nodes until we hit a childless node. (go depth until find latest child)
When we traverse down a path of children, we add them to the stack as we go along. Once we reach a node with no accessible children, we follow our path backwards until we find a node that has another path extending off of it

### Breadth-first
In breadth-first searches, we go broad first. This means that after we examine our first node, we examine all of its immediately neighboring nodes before we go any deeper.

### DFS vs BFS
In general, BFS is best for short searches.
DFS is good, then, if you're interested in checking out all possible paths from one point to another

Basically, use DFS if you want to exhaust all possible options, and use BFS if you want to find something as quickly as possible!


### What is “React Fiber”?
Fiber is the new reconciliation engine in React 16. Its main goal is to enable incremental rendering of the virtual DOM. 
The primary goal of Fiber is to enable React to take advantage of scheduling (the process of determining when work should be performed)
Specifically, we need to be able to
- pause work and come back to it later.
- assign priority to different types of work.
- reuse previously completed work.
- abort work if it's no longer needed.

[React Fiber with Example](https://medium.com/@armen_stepanyan/understanding-react-fiber-9a8412796ded)

### React Flow Overview
**1. Functional Components:**
- In React, functional components are JavaScript functions that return JSX (which looks like HTML). JSX is a syntactic sugar that lets you write HTML-like code inside JavaScript.
- When you write a React component, the function executes and returns a JSX representation of the UI, which React will render to the screen.
For Eaxmple
```ts
const Welcome = () => {
  return <h1>Welcome, User!</h1>;
}
```
The virtual DOM representation of the above JSX would look something like this:
```ts
{
    type: 'h1',
    props: {
        children: 'Welcome to React!'
    }
}
```

**2. Virtual DOM:**
- The virtual DOM is a lightweight in-memory representation of the actual DOM. It's a copy of the real DOM, but it's not rendered on the screen. Instead, it's used by React to optimize updates to the real DOM.
- When you return JSX from a functional component, React internally creates a virtual DOM tree that represents that JSX structure. React doesn't directly update the browser's DOM immediately. Instead, it works with the virtual DOM to calculate the minimal number of changes required.

**Key Idea**: The virtual DOM is a JavaScript object that mimics the structure of the HTML elements returned by your component.

**3. Reconciliation:**
- When the state or props change, React re-renders the component function. A new virtual DOM tree is created based on the updated JSX returned by the component.

- React then compares the new virtual DOM tree with the previous one using an algorithm called "reconciliation."

- It figures out the **diffs (differences)** between the two virtual DOM trees and applies the minimal set of updates to the real DOM. This process is what makes React efficient.

### In Simple Terms:
- A React component (functional component) returns JSX, which is just a syntax extension of JavaScript that looks like HTML.
- React turns the JSX into a virtual DOM—an internal representation of the UI.
- When changes occur (like state or props), React updates the virtual DOM first and then figures out the smallest set of changes to apply to the actual DOM.

  

### React Components & State
React is built around the concept of components. A component is essentially a JavaScript function (or class in older versions) that returns JSX (which looks like HTML) that React will render into the DOM.

Each time you call a component function (or class), React re-renders that component and generates new virtual DOM elements, which are then compared to the previous virtual DOM state. If any changes are detected, React updates the actual DOM to reflect the new state.

However, you don’t want to lose all of the data every time a component re-renders, especially when a user interacts with the UI, as it would be inefficient to start from scratch each time. This is where state comes in.

#### State in React (useState)
To keep track of data between renders, React introduces the concept of state. For functional components, useState is the most common hook that allows you to store and update values over time. When the state changes, React triggers a re-render to update the DOM with the new data.

For example:

```ts
const [count, setCount] = useState(0);
```

Here, count is a piece of state, and setCount is a function that updates the state. React will remember the value of count across renders, so even after a re-render, count will still hold its value.

#### Re-renders and Keeping Data
When a React component function is called, it re-executes. However, React doesn’t just throw away all your previous data. It ensures that state values persist across re-renders by maintaining a "reference" to them. That way, you don't lose state when React re-renders the component.

### Optimizing Re-renders
To further optimize and control how and when re-renders happen, React introduced hooks like `useMemo`, `useCallback`, and `useEffect`.
- useMemo:
This hook memoizes the result of an expensive function so that it doesn’t get recalculated unless the dependencies change.
It's useful for optimizing performance when a calculation depends on props or state and doesn't need to be recalculated on every render.

```ts
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

- useCallback
This hook returns a memoized version of the callback function that only changes if the dependencies change.
It's helpful for passing stable functions down to child components to avoid unnecessary re-renders.

```ts
const memoizedCallback = useCallback(() => { doSomething(a, b) }, [a, b]);
```

- useState
This hook allows you to persist state across re-renders without having to worry about losing local variables between render cycles.

```ts
const [state, setState] = useState(initialState);
```

### In Simple Terms
- Components: Functions that return JSX, which React renders into the DOM.

- State: Used to keep track of dynamic data across re-renders. useState lets you maintain the value of variables even when the component re-renders.

- Re-renders: React calls the component function again when state or props change. Without state, local variables would be reset each time.

- Optimization: React hooks like useMemo, useCallback, and useState help manage state and performance during re-renders.

### Create new app
```
npx create-react-app my-app
```

### Conditional Rendering
Set condition in `render` function
```typescript
import React, { useState } from 'react';

const Welcome = () => {
    const [isLoggedId, setIsLoggedId] = useState(true);

    return (
        <div>
            {isLoggedId ? <div>Welcome User</div> : <div>Welcome Guest</div>}
        </div>
    );
};

export default Welcome;
```
Or with variable
```typescript
render() {
    let message;
    if (this.state.isLoggedId) {
        message =  <div>Welcome User</div> 
    } else {
        message = <div>Welcome Guest</div> 
    }
    return <div>{message}</div>
   
}
```
With ternary operator
```typescript
return  this.state.isLoggedId ? <div>Welcome User</div> : <div>Welcome Guest</div> 
```
Or
```typescript
return this.state.isLoggedId && <div>Welcome User</div>
```

### Display array
```typescript
import React from 'react'

function List() {
const list = ['aaa', 'bbb', 'ccc'];
return (
    <div>
        <ul>
            {
                list.map(item => {
                    return <li key={item}>{item}</li>
                })
            }
        </ul>
    </div>
)
}

export default List
```
Or with variable
```typescript
function List() {
    const list = ['aaa', 'bbb', 'ccc', 'ddd'];
    const result = list.map(item => <li key={item}>{item}</li>);
    return (
        <div>
            <ul> {result} </ul>
        </div>
    )
}
```
#### Iterating array of object
Create `Person` component
```typescript
import React from 'react'

function Person({ person }) {
    return (
        <div>
            Name: <b>{person.name}</b> Age: {person.age}
        </div>
    )
}

export default Person
```
List.js
```typescript
import React from 'react';
import Person from './Person';

function List() {
    const personList = [
        {
            id: 1,
            name: 'John',
            age: 25
        },
        {
            id: 2,
            name: 'Bruce',
            age: 30,
        }
    ];
    const result = personList.map(person => <li key={person.id}><Person person={person} /></li>);
    return (
        <div>
            <ul> {result} </ul>
        </div>
    )
}

export default List
```

### Class and Styles
```html
<h1 className="primary">Style test</h1>
```
OR 
```typescript
function StyleSheet(props) {
   let className =  props.primary ? 'primary' : '';
    return (
        <div>
            <h1 className={className}>Style test</h1>
        </div>
    )
}
```

OR
```html
 <h1 className={`${className} custom-class`}>Style test</h1>
```

OR with style object
```typescript
function StyleSheet() {
    const heading = {
        color: 'red',
        fontSize: '42px'
    }
    return (
        <div>
            <h2 style={heading}>h2 header</h2>
        </div>
    )
}
```

#### Inline style
```html
<p style={{ backgroundColor: !isActive ? 'green' : 'gray' }}>{name}</p>
```

Component scoped styles. Create `css` file with `{name}.module.css` pattern

**appStyle.module.css**
```css
.green {
    background-color: green;
}
```

Now in `app.js` we need to import and use class names like this `styles.green`
```typescript
import styles from './appStyles.module.css';

function App() {
  return (
    <div className="App" className={styles.green}>
        <h1>Hello</h1>
    </div>
  );
}

export default App;

```

### Forms
Controlled Components. Making the React state to be the “single source of truth”. Then the React component that renders a form also controls what happens in that form on subsequent user input. An input form element whose value is controlled by React in this way is called a **controlled component**

Simple example
```typescript
import React, { useState } from 'react';

const Form = () => {
    const [username, setUsername] = useState('');
    const [topic, setTopic] = useState('react');

    const handleUsernameChange = (event) => {
        setUsername(event.target.value);
    };

    const handleTopicChange = (event) => {
        setTopic(event.target.value);
    };

    const handleSubmit = (event) => {
        event.preventDefault();
        console.log({ username, topic });
    };

    return (
        <form onSubmit={handleSubmit}>
            <div>
                <label>Username</label>
                <input type="text" value={username} onChange={handleUsernameChange} />
            </div>
            <div>
                <select value={topic} onChange={handleTopicChange}>
                    <option value='vue'>Vuejs</option>
                    <option value='react'>ReactJs</option>
                    <option value='angular'>Angular</option>
                </select>
            </div>
            <p>
                <button>Submit</button>
            </p>
            <p>{username}</p>
            <p>{topic}</p>
        </form>
    );
};

export default Form;

```

Function component
```typescript
import React, { useState } from 'react';

export default function Form() {

  const [name, setName] = useState("");
  return (
    <div>
      <input type="text" value={name} onChange={(e) => setName(e.target.value)} />
      <p>{name}</p>
    </div>
  );
}
```

### Counter example
With function using `useState`
```typescript
import React, { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  return (
    <div>
     <button onClick={() => setCount(prevState => prevState - 1)}>Decrement</button>
      {count}
      <button onClick={() => setCount(prevState => prevState + 1)}>Increment</button>
    </div>
  );
}

```

Same example with class
```typescript
import React from 'react';

export class Counter2 extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
    };
  }

  render() {
    return (
      <div>
        <button onClick={() => this.setState({ count: this.state.count - 1 })}>
          Decrement
        </button>
        {this.state.count}
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Increment
        </button>
      </div>
    );
  }
}
```

[Stackblitz](https://stackblitz.com/edit/a-react-counter?file=src/Counter.js)

### Install React with vite
```cmd
npm create vite@latest
```

### Passing output/call parent's function
```typescript
function App() {

  const userNameGenerated = (userName: string) => {
    console.log(userName)
  }

  return (
    <>
      <User defaultName='John' defaultLastname='Doe' userNameGenerated={userNameGenerated}/>
    </>
  )
}
```

Child `User` component
```typescript
interface InputData {
    defaultName: string;
    defaultLastname: string;
    userNameGenerated: (userName: string) => void
}

export function User({ defaultName = '', defaultLastname = '', userNameGenerated }: InputData){

    const [name, setName] = useState(defaultName);
    const [lastname, setLastname] = useState(defaultLastname);

    useEffect(() => {
        const userName = `${name}_${lastname}`.toLowerCase();
        // 'username' is ready, notify parent
        userNameGenerated(userName)
    }, [name, lastname, userNameGenerated])
    
    return (
        <>
            <h1>User</h1>
            <p>
                <input type="text" value={name} onChange={e => setName(e.target.value)} placeholder="Name"/>
            </p>
            <p>
                <input type="text" value={lastname} onChange={e => setLastname(e.target.value)} placeholder="Lastname"/>
            </p>
        </>
    )

}

```


