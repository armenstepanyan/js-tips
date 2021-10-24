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

