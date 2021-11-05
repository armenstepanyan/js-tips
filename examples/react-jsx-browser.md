Using react with jsx in browser
```
<!doctype html>

<html>
  <head>
    <script src="https://unpkg.com/react@17.0.2/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@17.0.2/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  </head>

  <body>
    <div id="root">
    </div>

    <script type="text/babel">
        const root = document.getElementById('root');
        let date = new Date().toLocaleString()
        const element = (
            <div className="container">
              Current time:  {date}
            </div>
        )
        ReactDOM.render(element, root);
    </script>  
  </body>
</html>

```
[Example](https://plnkr.co/edit/mAG9xAPfj3McQ107?open=index.html&preview)
