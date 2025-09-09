### valueOf
The **valueOf()** method returns the primitive value of the specified object.

```ts
var user = {
  firstName: 'John',
  age: 25,
  valueOf: function(){
    return this.age
  },
  toString: function() {
    return 'Name is  ' + this.firstName;
  }
};
```

```ts
alert( user )  // Name is John
console.log( 5 + user) // 30

```

### Symbol.toPrimitive
The Symbol.toPrimitive is a symbol that specifies a function valued property that is called to convert an object to a corresponding primitive value.
```ts
const obj = {
  [Symbol.toPrimitive](hint) {
    if (hint === 'number') {
      return 42;
    }
    if (hint == 'string') {
      return 'hello';
    }
    return true;
  }
}

```

Output
```ts
console.log(+obj2);     // 10        -- hint is "number"
console.log(`${obj2}`); // "hello"   -- hint is "string"
console.log(obj2 + ''); // "true"    -- hint is "default"
```

Test
```ts
Object.keys(obj) // []
Object.values(obj) // []
Object.getOwnPropertySymbols(obj) // [Symbol(Symbol.toPrimitive)]
Object.getOwnPropertyDescriptors(obj) //  { Symbol(Symbol.toPrimitive): {writable: true, enumerable: true, configurable: true, value: ƒ} }

```


### Symbol
A Symbol is a primitive data type introduced in ES6. It represents a unique, immutable value.

```ts
const sym = Symbol("description");
```
- The `description` is optional and only used for debugging (not to determine equality).
- Each call to `Symbol()` returns a **new and unique symbol**, even if the description is the same.

```ts
var sym1 = Symbol();
var sym2 = Symbol("foo");
var sym3 = Symbol("foo");

Symbol("foo") === Symbol("foo"); // false
````
Even though both symbols have the same description `("foo")`, they are not equal—each symbol is unique.

Symbols are often used as non-string keys in objects, which helps avoid accidental key collisions.
```ts
var id1 = Symbol("id");
var id2 = Symbol("id");
console.log(id1 === id2); // false

var user = {
 name: "Armen",
 [id]: 10,
 [id2]: 20
}


console.log(user[id1]) // 10 
console.log(user[id2]) // 20
```

- Symbols allow you to store hidden or private-like properties on objects.
- These keys won't show up in `for...in`, `Object.keys()`, or `JSON.stringify()`

### Global Symbols (Symbol.for())
While `Symbol()` always creates a new symbol, Symbol.for(key) checks a global symbol registry:
```ts
var id1 = Symbol.for("id");
var id2 = Symbol.for("id");
```
The result will return `true`
```ts
console.log(id1 === id2) // true
```

✅ How it works:
- If a symbol with the key "id" exists in the global symbol registry, it returns it.
- If it doesn't exist, it creates one and registers it under that key.

✅ Why use Symbol.for()?
- To share a symbol across files/modules (for instance, library and app code).
- To allow communication via known symbol keys.

#### Symbol.keyFor()
You can retrieve the key for a global symbol using Symbol.keyFor():

```ts
const sym = Symbol.for("myKey");
console.log(Symbol.keyFor(sym)); // "myKey"
```
**Note:** This only works with symbols from Symbol.for(). It will return undefined for local symbols.

### Object.create()
The `Object.create()` method creates a new object, using an existing object as the prototype of the newly created object.
Syntax
```ts
Object.create(proto)
Object.create(proto, propertiesObject)
```
`proto` - The object which should be the prototype of the newly-created object. The `proto` parameter has to be either
**null** or an **Object** excluding primitive wrapper objects (example: new String)
```ts
const obj = {
  log: function(){
    console.log(`Name is ${this.name}`)
  }
}

var foo = Object.create(obj);
foo.name = "John";
foo.log()  // Name is John
```
foo.log function comes from obj.log. So if we delete `delete obj.log` - `foo.log` will throw an error
```ts
console.log(foo)
{ 
  name: 'John'
  [[Prototype]]: Object
      log: f() // refers to obj.log
      [[Prototype]]: Object
}
```
Same example with `__proto__`
```ts
var o = {
  log: function(){
    console.log(this.name)
  }
}

var foo = {name: 'John'}
{ 
  name: 'John'
  [[Prototype]]: Object
}

foo.__proto__ = o;
// foo
{ 
  name: 'John'
  [[Prototype]]: Object
      log: f() // refers to obj.log
      [[Prototype]]: Object
}

foo.log() // John

```

### Object with null prototype
```ts
const nullProtoObj = Object.create(null);
console.log(nullProtoObj) // {} empty object - no prototype

o.toString() // Uncaught TypeError: o.toString is not a function
console.log("nullProtoObj is: " + nullProtoObj) // Uncaught TypeError: Cannot convert object to primitive value
```

Define `toString` for `Object.create(null)`
```ts
let obj = Object.create(null, {
  toString: { 
    value() { 
      return Object.values(this).join();
    },
    enumerable: false // do not display toString in for..in, Object.keys, Object.values
  }
});

obj.a = 1;
obj.b= 2;
console.log(dictionary.toString()) // 1,2
```


### Object.defineProperty
The JavaScript Object.defineProperty() method adds or modifies existing property on an object and returns the object.
Syntax
```ts
Object.defineProperty(obj, prop, descriptor)
```
Property descriptors present in objects come in two ways: **data descriptors** and **accessor descriptors**. 
A data descriptor is a property that has a value, which may or may not be writable. An accessor descriptor is a property described by a getter-setter pair of functions. A descriptor must be one of these two flavors; it cannot be both.

- configurable 
when this is set to `false` the type of this property cannot be changed between data property and accessor property, the property may not be deleted
Defaults: `false`

- enumerable
if `true` property will be shown in `for..in` and `Object.keys()`. Defualt: `false`

- writable
`true` value can be changed. Defualt: `false`

- value
The value associated with the property. Can be any valid JavaScript value (number, object, function, etc). Defaults to `undefined`

```ts
// simple defination
var user = {};
user.name = 'John'

// same with descriptor
Object.defineProperty(user, "name", { 
  value: "John", 
  configurable: true, // can be deleted
  writable: true,    // value can be change
  enumerable: true   // will be displayed in for..in 
}
);

user.name = "Bob"; // {name: 'Bob'}
delete user.name // {}

```

To make property as constant use `writable` and `configurable`
```ts
var user = {};
Object.defineProperty(user, "name", { 
  value: "John", 
  configurable: false, // disallow "delete user.name"
  writable: false,    // cannot change, disallow "user.name="
}
);

user.name = 'Bob'; // {name: 'John'} <-- unchanged
```
In strict mode `user.name = '...'` will throw an error. (Cannot assign to read only property 'name' of object)
