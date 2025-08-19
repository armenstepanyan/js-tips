## Utility Types & Questions

### Partial<Type>
Constructs a type with all properties of `Type` set to optional. This utility will return a type that represents all subsets of a given type.
```ts
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
```ts
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
```ts
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

### Pick<Type, Keys>
Constructs a type by picking the set of properties Keys from Type. This will get get only specified properties from given type and create new one.
```ts
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = Pick<Todo, "title" | "completed">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};
```

### Omit<Type, Keys>
Constructs a type by picking all properties from Type and then removing Keys.
```ts
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = Omit<Todo, "description">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};

```

### Question 1:
What is the difference between unknown and any in TypeScript? Which one is safer to use and why?

### Answer:
- `any` disables all type checking. Once a variable is typed as `any`, you can perform `any` operation on it without TypeScript checking for correctness.

```ts
let a: any = 10;
a.toUpperCase(); // OK, even if a is a number

```

- `unknown` is a type-safe counterpart to any. You cannot perform operations on an unknown value until you do some type checking or assertion.

```ts
let b: unknown = 10;
b.toUpperCase(); // ❌ Error
if (typeof b === 'string') {
  b.toUpperCase(); // ✅ OK
}

```
✅ Safer to use: `unknown`
It forces you to validate the type before using it, reducing the chances of runtime errors.


### Question 2:
What are conditional types in TypeScript? Can you write an example of a conditional type that removes null and undefined from a type?

### Answer:
Conditional types allow you to express logic within types. They follow the form:

```ts
A extends B ? C : D

```

✅ Use case: Removing null and undefined from a type.

```ts
type NonNullable<T> = T extends null | undefined ? never : T;

type A = string | null | undefined;
type B = NonNullable<A>; // B is string
```

#### Explanation:
If T is `null` or `undefined`, it resolves to never.

Otherwise, it keeps T.

This filters out `null` and `undefined` from unions.
```ts
type UserInput = string | null | undefined;

// We want to make sure we work only with the non-nullable part (i.e., string)
function handleInput(input: NonNullable<UserInput>) {
  // Now 'input' is guaranteed to be a string
  console.log("User typed:", input.toUpperCase());
}

// ✅ Valid usage:
handleInput("hello"); // Works fine

// ❌ Invalid usage:
// handleInput(null);        // Error!
// handleInput(undefined);   // Error!

```

### Question 3:
What’s the difference between an `interface` and a type alias in TypeScript? Are there any scenarios where one is strictly better than the other?

### Answer:
Both interface and type can describe the shape of an object, but they have some key differences:

✅ Differences:
1. Extension and Declaration Merging:

- interface supports declaration merging.
- type does not.

```ts
interface A {
  x: number;
}

interface A {
  y: number;
}

const obj: A = { x: 1, y: 2 }; // OK due to merging

type B = { x: number };
type B = { y: number }; ❌ Error: Duplicate identifier
```

2. Usage Scope:
- type can be used for primitive, union, intersection, and tuple types.
- interface is limited to object-like structures.

```ts
type Status = 'success' | 'error'; // ✅
interface Status = 'success' | 'error'; // ❌ Invalid
```

3. Extending:
Both interface and type can be extended, but the syntax is slightly different.

```ts
interface C extends A {}

type D = B & { y: number };

```

### Question 4:
Can you explain what mapped types are in TypeScript? Create a mapped type that makes all properties of a given type optional, except for one specific key.

### Answer:
Mapped types allow you to transform each property in a type using a consistent rule.

Basic example:

```ts
type Optional<T> = {
  [K in keyof T]?: T[K];
};

```

We want to make all properties optional except for one key.
```ts
type AllOptionalExcept<T, K extends keyof T> = {
  [P in keyof T as P extends K ? P : never]-?: T[P]; // Required
} & {
  [P in keyof T as P extends K ? never : P]?: T[P]; // Optional
};
```

Usage

```ts
type User = {
  id: number;
  name: string;
  email: string;
};

type UserWithRequiredId = AllOptionalExcept<User, 'id'>;
/*
Resulting type:
{
  id: number;         // required
  name?: string;
  email?: string;
}
*/

```

🧠 Breakdown:
- We use key remapping (as) to split the keys into required vs optional groups.

-  `-?` ensures the required key is not optional.

### Question 5:
How can you enforce immutability on a TypeScript object deeply, not just at the top level? Provide a utility type that makes all nested properties readonly.

### Answer:
TypeScript’s built-in `Readonly<T>` only applies to the **first level** of properties:

```ts
type User = {
  id: number;
  profile: {
    name: string;
    email: string;
  };
};

const user: Readonly<User> = {
  id: 1,
  profile: {
    name: 'Alice',
    email: 'alice@example.com'
  }
};

user.id = 2; // ❌ Error
user.profile.name = 'Bob'; // ✅ No error — because profile is still mutable!

```

✅ Deep readonly utility type:

```ts
type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends object
    ? DeepReadonly<T[P]>
    : T[P];
};
```

✅ Example usage:
```ts
const deepUser: DeepReadonly<User> = {
  id: 1,
  profile: {
    name: 'Alice',
    email: 'alice@example.com'
  }
};

deepUser.id = 2; // ❌ Error
deepUser.profile.name = 'Bob'; // ❌ Error
```

### Question 6:
Explain the difference between interface and abstract class in TypeScript. When would you use one over the other?

### Answer:
Both interface and abstract class are used to define contracts or blueprints for other classes, but they have key differences:

✅ Interface:
- Only describes the shape of an object.
- Can only declare method signatures and properties (no implementations).
- Supports multiple inheritance (a class can implement multiple interfaces).
- Compiled away — no runtime code generated.
- Supports declaration merging.

```ts
interface Logger {
  log(message: string): void;
}

```

✅ Abstract Class:
- Can provide both method signatures and implementations.
- Supports constructor, state (fields), and behavior.
- Allows single inheritance only.
- Exists at runtime (you can use instanceof).
- Used when you want to share code across subclasses.

```ts
abstract class Logger {
  abstract log(message: string): void;

  printDate() {
    console.log(new Date());
  }
}

```

### Run
```
- tsc `file.ts`
- node `file.js`
```
