## Three Principles
Redux can be described in three fundamental principles:

### Single source of truth
The global state of your application is stored in an object tree within a single store

### State is read-only
The only way to change the state is to emit an action, an object describing what happened.

### Changes are made with pure functions
To specify how the state tree is transformed by actions, you write pure reducers.

**Store** holds the state of your application
**Action**  describes the changes in the state of the application
**reducer**  carries out the state transition depending on the actions

Create react app
```
npx create-react-app react-redux-demo
```

Install `react-redux`
```
npm install redux react-redux
```

Start app
```
npm start
```

Create `src\redux\cake\cakeActions.js` file
```
import { BUY_CAKE } from "./cakeTypes"

export const buyCake = () => {
    return {
        type: BUY_CAKE
    }
}
```

Create `src\redux\cake\cakeTypes.js` file
```
export const BUY_CAKE = '';
```

Create `src\redux\cake\cakeReducer.js` reducer file
```
import { BUY_CAKE } from "./cakeTypes"
const initialState = {
    numOfCakes: 10
}

const cakeReducer = (state = initialState, action) => {
    switch(action.type){
        case BUY_CAKE: return {
            ...state,
            numOfCakes: state.numOfCakes - 1
        }
        default: return state;
    }
}

export default cakeReducer
```


Create `src\redux\store.js` store
```
import { createStore } from 'redux'
import cakeReducer from './cake/cakeReducer'

const store = createStore(cakeReducer)

export default store
```

Import `Provider` in `App.js`. Wrap view into `Provider` and pass `store` as props

```
import { Provider } from 'react-redux';
import store from './redux/store';
import CakeContainer from './components/CakeContainer';

function App() {
  return (
    <Provider store={store}>
    <div className="App">
          <CakeContainer />
     </div>
    </Provider>
   
  );
}

export default App;
```

CakeContainer.js
- create `mapStateToProps` function
```
const mapStateToProps = state => {
    return {
        numOfCakes: state.numOfCakes
    }
}
```

- create `mapDispatchToProps` function
```
const mapDispatchToProps = dispatch => {
    return {
        buyCake: () => dispatch(buyCake())
    }
}
```

- connect
```
export default connect(mapStateToProps, mapDispatchToProps)(CakeContainer)
```

Now CakeContainer component will receive 2 new props called `numOfCakes` and `buyCake()`
```
import React from 'react'
import { buyCake } from '../redux';
import { connect } from 'react-redux';

function CakeContainer(props) {
    return (
        <div>
            <h2>Number of cakes - {props.numOfCakes}</h2>
            <button onclick={props.buyCake}>Buy cake</button>
        </div>
    )
}

const mapStateToProps = state => {
    return {
        numOfCakes: state.numOfCakes
    }
}

const mapDispatchToProps = dispatch => {
    return {
        buyCake: () => dispatch(buyCake())
    }
}

export default connect(mapStateToProps, mapDispatchToProps)(CakeContainer)
```

### Using hooks in function components
`useDispatch` hook return a reference from dispatch function from the redux store
```
import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { buyCake } from '../redux';

function HooksCakeContainer() {
   const  numOfCakes = useSelector(state => state.numOfCakes);
   const dispatch = useDispatch();
    return (
        <div>
           <h2>Num of cakes {numOfCakes}</h2> 
           <button onClick={() => dispatch(buyCake())}>Bauy cake</button>
        </div>
    )
}

export default HooksCakeContainer
```

### Example with async action
```
const redux = require('redux')
const createStore = redux.createStore;
const applyMiddleware = redux.applyMiddleware;
const thunkMiddleware = require('redux-thunk').default
const axios = require('axios')

const initialState = {
    loading: false,
    users: [],
    error: ''
}

const FETCH_USER_REQUEST = 'FETCH_USER_REQUEST';
const FETCH_USER_SUCCESS = 'FETCH_USER_SUCCESS';
const FETCH_USER_FAILURE = 'FETCH_USER_FAILURE';

const fetchUsersRequest = () => {
    return {
        type: FETCH_USER_REQUEST
    }
}

const fetchUsersSuccess = users => {
    return {
        type: FETCH_USER_SUCCESS,
        payload: users
    }
}

const fetchUsersFAILURE = error => {
    return {
        type: FETCH_USER_FAILURE,
        payload: error
    }
}

const reducer = (state = initialState, action) => {
    console.log(action)
    switch(action.type){
        case FETCH_USER_REQUEST:
            return {
                ...state,
                loading: true
            }
        case FETCH_USER_SUCCESS:
            return {                
                loading: false,
                users: action.payload,
                error: '',
            }
        case FETCH_USER_FAILURE:
            return {                
                loading: false,
                users: [],
                error: action.payload,
            }    
    }
}

// thunk middleware return function instead of action object
const fetchUsers = () => {
    console.log('start')
    return function(dispatch) {
        dispatch(fetchUsersRequest())
        axios.get('https://jsonplaceholder.typicode.com/users')
        .then(resp => {
            const users = resp.data.map(user => user.id)
            dispatch(fetchUsersSuccess(users))
        }).catch(error => {
            // error.message
            dispatch(fetchUsersFAILURE(error.message))
        })
    }
}


const store = createStore(reducer, applyMiddleware(thunkMiddleware))
store.subscribe(() => { console.log(store.getState()) })
store.dispatch(fetchUsers())
```

