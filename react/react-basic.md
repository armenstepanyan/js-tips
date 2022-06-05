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



### Create new app
```
npx create-react-app my-app
```

### Conditional Rendering
Set condition in `render` function
```
export default class Welcome extends Component {

    constructor(props) {
        super(props)
    
        this.state = {
             isLoggedId: true,
        }
    }
    

    render() {
        if (this.state.isLoggedId) {
            return <div>Welcome User</div> 
        } else {
           return <div>Welcome Guest</div> 
        }
       
    }
}
```
Or with variable
```
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
```
    render() {

        return (
            this.state.isLoggedId ?
            <div>Welcome User</div> :
            <div>Welcome Guest</div> 
        )
    }
```
Or
```
return this.state.isLoggedId && <div>Welcome User</div>
```

### Display array
```
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
```
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
```
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
```
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
```
<h1 className="primary">Style test</h1>
```
OR 
```
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
```
 <h1 className={`${className} custom-class`}>Style test</h1>
```

OR with style object
```
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

Component scoped styles. Create `css` file with `{name}.module.css` pattern

**appStyle.module.css**
```
.green {
    background-color: green;
}
```

Now in `app.js` we need to import and use class names like this `styles.green`
```
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
```
export class Form extends Component {
    constructor(props) {
        super(props)
    
        this.state = {
             username: '',
             topic: 'react'
        }
    }
    
    handleUsernameChange = event => {
        this.setState({
            username: event.target.value
        })
    }

    handleTopicChange = event => {
        this.setState({
            topic: event.target.value
        })
    }

    handleSubmit = event => {
        event.preventDefault();
        console.log(this.state)
    }

    render() {
        return (
            <form onSubmit={this.handleSubmit}>
                <div>
                    <label>Username</label>
                    <input 
                        type="text" 
                        value={this.state.username} 
                        onChange={this.handleUsernameChange}
                    />
                </div>
                <div>
                    <select value={this.state.topic} onChange={this.handleTopicChange}>
                        <option value='vue'>Vuejs</option>
                        <option value='react'>ReactJs</option>
                        <option value='angular'>Angular</option>
                    </select>
                    </div>
                    <p>
                        <button>Submit</button>
                    </p>
                <p>{this.state.username}</p>
                <p>{this.state.topic}</p>
            </form>
        )
    }
}

export default Form
```

### Counter example
With function using `useState`
```
import React, { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
      {count}
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

```

Same example with class
```
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


