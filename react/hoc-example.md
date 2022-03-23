A higher-order component (HOC) is an advanced technique in React for reusing component logic. HOCs are not part of the React API. They are a pattern that emerges from React’s
 compositional nature.

User component is displaying only information of given user
```
import React from 'react';

const User = ({ user }) => {
  return (
    <div>
      <p>ID: {user.id}</p>
      <p>Name: {user.name}</p>
      <p>Email: {user.email}</p>
      <hr />
    </div>
  );
};

export default User;
```

Now create HOC component which will fetch user data and pass it to User component
```
import React, { useState, useEffect } from 'react';

const withCurrentUserId = (Component) => {
  // dectructuring userId and collect rest properties in ...props
  const WrapComponent = ({ userId, ...props }) => {
    const [user, setUser] = useState(null);

    useEffect(() => {
      fetch(`https://jsonplaceholder.typicode.com/users/${userId}`)
        .then((response) => response.json())
        .then((user) => setUser(user));
    }, []);

    if (!user) {
      return 'Loading...';
    }

    return <Component {...props} user={user} />;
  };

  return WrapComponent; // <-- return wrapped component
};

export default withCurrentUserId;
```

Usage

```
import React from 'react';
import './style.css';
import User from './User';
import withUserId from './withUserId';

const SpecificUser = withUserId(User);

export default function App() {
  return (
    <div>
      <SpecificUser userId={1} />
      <SpecificUser userId={2} />
    </div>
  );
}
```
[Example Stackblitz](https://stackblitz.com/edit/a-react-hoc?file=src/App.js)

