### Partial<Type>
Constructs a type with all properties of `Type` set to optional. This utility will return a type that represents all subsets of a given type.
```
interface Todo {
    id: number;
    title: string;
    description: string;
  }

  function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>) {
    return { ...todo, ...fieldsToUpdate };
  }

  const todo1: Todo = {
    id: 1,
    title: 'My todo 1',
    description: 'Simple todo list'
  };
  
  const todo2 = updateTodo(todo1, {
    title: "My todo 2",
  });

  console.log(todo2)
  ```
  Result
  ```
 { id: 1, title: 'My todo 2', description: 'Simple todo list' }
```

### Readonly<Type>
Constructs a type with all properties of Type set to readonly, meaning the properties of the constructed type cannot be reassigned.
```
  interface Country {
      code: string;
      phoneCode: string;
  }

  const RussiaCountry: Readonly<Country> = {
    code: 'ru',
    phoneCode: '+7'
  } 

  // invalid: Cannot assign to 'code' because it is a read-only property
  RussiaCountry.code = 'r';
```

### Record<Keys,Type>
Constructs a type with a set of properties Keys of type Type. This utility can be used to map the properties of a type to another type.
```
interface PageInfo {
  title: string;
}

type Page = "home" | "about" | "contact";

const nav: Record<Page, PageInfo> = {
  about: { title: "about" },
  contact: { title: "contact" },
  home: { title: "home" },
};
console.log(nav.about)

// this will not works:
// An index signature parameter type cannot be a union type. Consider using a mapped object type instead.
 const nav2: { 
      [key: Page]: PageInfo
     }
```


