The **valueOf()** method returns the primitive value of the specified object.

```
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

```
alert( user )  // Name is John
console.log( 5 + user) // 30

```

### Symbol.toPrimitive
The Symbol.toPrimitive is a symbol that specifies a function valued property that is called to convert an object to a corresponding primitive value.
```
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
```
console.log(+obj2);     // 10        -- hint is "number"
console.log(`${obj2}`); // "hello"   -- hint is "string"
console.log(obj2 + ''); // "true"    -- hint is "default"
```

### Symbol
```
var sym1 = Symbol();
var sym2 = Symbol("foo");
var sym3 = Symbol("foo");

Symbol("foo") === Symbol("foo"); // false
````

```
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

Global symbols
```
var id1 = Symbol.for("id");
var id2 = Symbol.for("id");
```
The result will return `true`
```
console.log(id1 === id2) // true
```

### Object.defineProperty
The JavaScript Object.defineProperty() method adds or modifies existing property on an object and returns the object.
Syntax
```
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

```
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
```
var user = {};
Object.defineProperty(user, "name", { 
  value: "John", 
  configurable: false, // disallow "delete user.name"
  writable: false,    // cannot change, disallow "user.name="
}
);

user.name = 'Bob'; // {name: 'John'} <-- unchanged
```
In strict mode `user.name = '...'` will trhow an error. (Cannot assign to read only property 'name' of object)
