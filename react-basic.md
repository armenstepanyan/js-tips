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




