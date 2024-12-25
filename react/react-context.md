### React Context
React Context is a feature that allows you to share state and data across your component tree without having to pass props down manually at every level. It is particularly useful for global data that needs to be accessed by many components at different nesting levels, such as user authentication status, theme settings, or language preferences.

#### Step 1: Create a Context
UserContext.tsx
```ts
import React, { createContext, useState, useContext, ReactNode } from 'react';

interface UserContextType {
  userName: string;
  setUserName: (name: string) => void;
}

const UserContext = createContext<UserContextType | undefined>(undefined);

export const UserProvider = ({ children }: { children: ReactNode }) => {
  const [userName, setUserName] = useState('test');

  return (
    <UserContext.Provider value={{ userName, setUserName }}>
      {children}
    </UserContext.Provider>
  );
};

export const useUser = () => {
  const context = useContext(UserContext);
  if (!context) {
    throw new Error('useUser must be used within a UserProvider');
  }
  return context;
};
```

#### Step 2: Wrap Your App with the Provider
main.tsx

```ts
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import App from './App.tsx'
import { UserProvider } from './UserContext';

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <UserProvider>
      <App />    
    </UserProvider>
  </StrictMode>,
)
```

#### Step 3: Use the Context in Your Components
```ts
import { useEffect, useMemo, useState } from "react";

import "./App.css";
import User from "./User";
import { useUser } from "./UserContext";

function App() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("test name");

  const { userName, setUserName } = useUser();

  return (
    <>
      <div>
        <h2>App component</h2>
        <p>
          <label htmlFor="username">Update User Name</label>
          <input
            type="text"
            id="username"
            value={userName}
            onChange={(e) => setUserName(e.target.value)}
          />
        </p>
      </div>
      <div>
        <h2>User component</h2>
        <User />
      </div>
    </>
  );
}

export default App;
```

User.tsx
```ts
import { useUser } from "./UserContext";

function User() {
  const { userName } = useUser();
    return ( 
       <>
          <div>
            Username from context is: <b>{userName}</b>
          </div>
       </>
     );
}

export default User;
```
