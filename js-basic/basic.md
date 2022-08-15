### Hoisting

Hoisting is JavaScript's default behavior of moving declarations to the top
```
x = 5; // Assign 5 to x

console.log(x);

var x // declare x (x  is undefined)
```

Variables defined with **let** and **const** are hoisted to the top of the block, but not `initialized`
Using a let variable before it is declared will result in a **ReferenceError**

```
x = 15;
let x;
// Uncaught ReferenceError: Cannot access 'x' before initialization
```

### JavaScript Initializations are Not Hoisted
JavaScript only hoists declarations, not initializations

```
var x = 5; // Declare and Initialize x
console.log(x) // 5
```

Initialization will not be hoisted

```
console.log(y); // undefined
var y = 5; // Initialize y 
```
### Strict mode
JavaScript in strict mode does not allow variables to be used if they are not declared

```
for (let i=0; i<10; i++) {
	console.log( i );
}

console.log( i ); // ReferenceError
```

Throw SyntaxError for re-declaration due to `var` being hoisted to the top of the block.
```
let x = 1;

{
  var x = 2; // SyntaxError for re-declaration
}
```

In switch statement if there is no block it will throw an error
```
let x = 1;
switch(x) {
  case 0:
    let foo;
    break;

  case 1:
    let foo; // SyntaxError for redeclaration.
    break;
}
```
However, it's important to point out that a block nested inside a case clause will create a new block scoped lexical environment, which will not produce the redeclaration errors shown above
```
let x = 1;

switch(x) {
  case 0: {
    let foo;
    break;
  }
  case 1: {
    let foo;
    break;
  }
}

```
### Temporal dead zone (TDZ)
A let or const variable is said to be in a "temporal dead zone" (TDZ) from the start of the block until code execution reaches the line where the variable is declared.

While inside the TDZ, the variable has not been initialized with a value, and any attempt to access it will result in a ReferenceError. The variable is initialized with a value when execution reaches the line of code where it was declared. If no initial value was specified with the variable declaration, it will be initialized with a value of undefined.

This differs from var variables, which will return a value of undefined if they are accessed before they are declared. The code below demonstrates the different result when let and var are accessed in code before the line in which the are declared.

```
{ 
  // TDZ starts at beginning of scope
  console.log(bar); // undefined
  console.log(foo); // ReferenceError
  var bar = 1;
  let foo = 2; // End of TDZ (for foo)
}
```

The term "temporal" is used because the zone depends on the order of execution (time) rather than the order in which the code is written (position). For example, the code below works because, even though the function that uses the let variable appears before the variable is declared, the function is called outside the TDZ
```
{
    // TDZ starts at beginning of scope
    const func = () => console.log(letVar); // OK

    // Within the TDZ letVar access throws `ReferenceError`

    let letVar = 3; // End of TDZ (for letVar)
    func(); // Called outside TDZ!
}

```

The following code results in a ReferenceError at the line shown:
```
function test(){
   var foo = 33;
   if(foo) {
      let foo = (foo + 55); // ReferenceError
   }
}
test();
```

The if block is evaluated because the outer var foo has a value. 
However due to lexical scoping this value is not available inside the block: the identifier foo inside the if block is the let foo. 
The expression `(foo + 55)` throws a ReferenceError because initialization of `let foo` has not completed — it is still in the temporal dead zone.

### Arrow functions
- has no own this

```
let group = {
  title: "Our Group",
  students: ["John", "Pete", "Alice"],

  showList() {
    this.students.forEach(
      student => console.log(this.title + ': ' + student) // this refers to `group`
    );
  },
  showList2() {
    this.students.forEach(function(student) {
     
      console.log(this) // this refers to window, not to group object
    });
  }
  
};
```

- has no `arguments`
```
const log = () => {
  console.log(arguments) // Uncaught ReferenceError: arguments is not defined
}
```

This can be useful when we need to create defered function
```
function defer(f, ms) {
  return function() {
    setTimeout(() => f.apply(this, arguments), ms)
  };
}

function sayHi(who) {
  alert('Hello, ' + who);
}

let sayHiDeferred = defer(sayHi, 2000);
sayHiDeferred("John");  // Hello, John after 2 seconds
```

Same example without arrow function
```
function defer(f, ms) {
  return function(...args) { // <--- keep arg here
    let ctx = this; // <-- keep context
    setTimeout(function() {
      return f.apply(ctx, args);
    }, ms);
  };
}
```

- cannot be called with `new` keyword
```
var log = () => {
  console.log('works')
}

var d = new log(); // Uncaught TypeError: log is not a constructor
```

### Call/apply/bind
The main concept behind all this methods is **Function burrowing**. 
Function borrowing allows us to use the methods of one object on a different object without having to make a copy of that method and maintain it in two separate places.
- **Call** invokes the function immediately and allows you to pass in arguments one by one
- **Apply** invokes the function immediately and allows you to pass in arguments as an array.
- **Bind** returns a new function, and you can invoke/call it anytime you want by invoking a function.

**call** attaches **this** into function and executes the function immediately:
```
var person = {  
  name: "James Smith",
  hello: function(thing) {
    console.log(this.name + " says hello " + thing);
  }
}

person.hello("world");  // output: "James Smith says hello world"
person.hello.call({ name: 'John Doe' }, 'world') // output: "John Doe says hello world"
```
Same example with **apply**
```
person.hello.apply({ name: 'John Doe' }, ['world'])
```
**bind** attaches **this** into function and it needs to be invoked separately:
```
person.hello("world");  // output: "James Smith says hello world"
var helloFunc = person.hello.bind({ name: "John Doe" });
helloFunc("world");  // output: John Doe says hello world"
```
In simple words, bind creates the function, call and apply executes the function.

### Function declaration vs. Function expression
Differences
- Hoisting:

Function declarations are similar to `var` they will be automatically hoisted, whereas function expressions will not be hoisted.

- Callback:

If a callback is a `function declaration`, it will be available in the global scope, which is not necessary most of the time.
If a callback is a `function expression`, it will not be available outside of the function that uses it.

- IIFE:

An Immediately-invoked Function Expression **(IIFE)** is a way to execute functions immediately, as soon as they are created.
As the name suggests, IIFEs can be created using function expression.

Another  difference between a function expression and a function declaration is the **function name**, which can be omitted in function expressions to create anonymous functions.
 The variable to which the function expression is assigned will have a `name` property. The name doesn't change if it's assigned to a different variable. 
 If function name is omitted, it will be the variable name (implicit name).  If function name is present, it will be the function name (explicit name).
  ```
const foo = function () {};
foo.name // "foo"

const foo2 = foo;
foo2.name // "foo"

foo2.name = "another name";
foo2.name // foo, name is unchangeable
 ```
 With named function
 ```
const bar = function baz() {};
bar.name // "baz"

console.log(foo === foo2); // true
console.log(typeof baz); // undefined
console.log(bar === baz); // false (errors because baz == undefined)
 ```
 
 ### AsyncFunction
 In JavaScript, every asynchronous function is actually an `AsyncFunction` object.
 ```
 async function a(){
    return await 1;
}
a.constructor.name // AsyncFunction
 ```
 Create function using `AsyncFunction` constructor
 ```
 const AsyncFunction = (async function () {}).constructor;
 const fn = new AsyncFunction(
  'a',
  'b',
  'return await Promise.resolve(a) + await Promise.resolve(b);',
);
 
 fn(10, 20).then((v) => {
  console.log(v); // prints 30
});
 ```
 
