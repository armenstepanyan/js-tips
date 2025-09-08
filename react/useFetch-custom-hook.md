Building your own Hooks lets you extract component logic into reusable functions.

Example of create `useFetch` custom hook that make get request to given url

**useFetch.js**
```ts
import { useState, useEffect } from 'react';

const useFetch = (url, initialData) => {
  const [data, setData] = useState(initialData);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(false);

  useEffect(() => {
    setLoading(true);
    fetch(url)
      .then((res) => res.json())
      .then((res) => {
        console.log(res);
        if (res.error) {
          setError(true);
        } else {
          setData(res);
        }
      })
      .catch((err) => {
        setError(true);
      })
      .finally(() => setLoading(false));
  }, []);

  return { data, loading, error };
};

export default useFetch;
```

Usage in App.js
```ts
import useFetch from './useFetch';

export default function App() {
  const url = 'https://random-data-api.com/api/users/random_user';
  const { data, loading, error } = useFetch(url, null);

  return (
    <div>
      <h1>User info</h1>
      {loading && <p>Loading... </p>}
      {data && (
        <p>
          {data?.first_name} {data?.last_name}{' '}
        </p>
      )}
      {error && <p>{error}</p>}
    </div>
  );
}
```

[Example](https://stackblitz.com/edit/react-4kfoqo?file=src/App.js)
