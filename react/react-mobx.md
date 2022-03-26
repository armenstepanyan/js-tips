Install mobx

[Stackblitz example](https://stackblitz.com/edit/a-react-mobx?file=src/App.js)
```
yarn add mobx
yarn add mobx-react
```

Create simple store
```
const StoreContext = React.createContext();

const StoreProvider = ({ children }) => {
  const store = useLocalStore(() => ({
    list: ['Item 1'],
    addItem: item => {
      store.list.push(item)
    },
    get itemCount() {
      return store.list.length;
    }
  }));

  return (
    <StoreContext.Provider value={store}>{children}</StoreContext.Provider>
  )
}
```

Display list. `useObserver` to observe changes in the store 
```
const ItemList = () => {
  const store = React.useContext(StoreContext);
  return useObserver(() => {
    return(
      <ul>
      {store.list.map(item => (<li key={item}>{item}</li>))}
    </ul>
    )
  })

}
```

Add new item form
```
const ItemForm = () => {
  const store = React.useContext(StoreContext);
  const [item, setItem] = React.useState('');
  return (
    <form onSubmit={e => {
      e.preventDefault();
      store.addItem(item);
      setItem('');
      }}>
      <input type="text" value={item} onChange={e => { setItem(e.target.value) }}/>
      <button type="submit">Add item</button>
    </form>
  )
}
```

Display computed value
```
const ItemHeader = () => {
  const store = React.useContext(StoreContext);
  return useObserver(() => (<h2>Total: {store.itemCount}</h2>))
}
```

App.js
```
function App() {
  return (
    <StoreProvider>
    <div className="App">
        <ItemHeader/>
        <ItemList />
        <ItemForm />
     </div>
    </StoreProvider>
    
  );
}

export default App;
```

