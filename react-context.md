Create ThemeContext.js
`{this.props.children}` reffers the child elements that wraps `<ThemeContextProvider>`

```
import React, { createContext, Component } from 'react';

export const ThemeContext = createContext();

class ThemeContextProvider extends Component {
    state = {
        isLightTheme: true,
        light: { syntax: '#555', ui: '#ddd', bg: '#eee' },
        dark:  { syntax: '#ddd', ui: '#333', bg: '#555' }
    }

    render() {
        return (
            <ThemeContext.Provider value={{...this.state}}>
                {this.props.children}
            </ThemeContext.Provider>
        );
    }
}

export default ThemeContextProvider;
```
App.js

```
import ThemeContextProvider from "./contexts/ThemeContext";
import { Navbar } from "./Navbar";

function App() {
  return (
      <ThemeContextProvider>
        <Navbar/>
      </ThemeContextProvider>      
    </div>
  );
}

export default App;
```


Navbar.js - use provided context data

```
import React, { Component } from 'react'
import { ThemeContext } from './contexts/ThemeContext'

export class Navbar extends Component {
    static contextType = ThemeContext;

    render() {
        console.log(this.context);
        const { isLightTheme, light, dark } = this.context;
        const theme = isLightTheme ? light : dark;
        return (
            <div style={{ background: theme.ui, color: theme.syntax }}>
                <ul>
                    <li>Home</li>
                    <li>Dashboard</li>
                </ul>
            </div>
        )
    }
}

export default Navbar
```

### Using Context.Consumer
```
export class Navbar extends Component {


    render() {
        
     return (
        <ThemeContext.Consumer>{(context) => {
            const { isLightTheme, light, dark } = context;
            const theme = isLightTheme ? light : dark;
            return (
            <div style={{ background: theme.ui, color: theme.syntax }}>
                <ul>
                    <li>Home</li>
                    <li>Dashboard</li>
                </ul>
            </div>
            )
            
        }}
        </ThemeContext.Consumer>
      )
        
        
    }
}

export default Navbar
```

### useContext for function components
```
  <ThemeContextProvider>
    <FunctionContext />
  </ThemeContextProvider>
```

```
import React, { useContext } from 'react';
import { ThemeContext } from './contexts/ThemeContext'

const FunctionContext = () => {
    const { isLightTheme, light, dark } = useContext(ThemeContext);
    const theme = isLightTheme ? light : dark;

    return (
       
        <div style={{background: theme.bg, color: theme.syntax}}>
            Some text
        </div>
    )
}

export default FunctionContext
```
