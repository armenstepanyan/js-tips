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

