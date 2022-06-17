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
