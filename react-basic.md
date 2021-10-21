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


