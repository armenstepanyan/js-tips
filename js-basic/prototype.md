### Prototype Inheritance in JavaScript (Quick Reference)

Prototype inheritance is the mechanism in JavaScript where objects inherit properties and methods from other objects through a prototype chain.

- Every JavaScript object has an internal [[Prototype]] (accessible via __proto__), which points to another object.
- If a property or method is not found on the object itself, JavaScript looks for it in its prototype chain.
- Functions have a .prototype property, which is used to set the [[Prototype]] of new objects when created using the new keyword.
- Shared methods and properties are typically added to the constructor function’s .prototype, so all instances can access them.

```ts
function Animal() {}
Animal.prototype.speak = function() {
  console.log("Animal speaks");
};

const dog = new Animal();
dog.speak(); // Inherited from Animal.prototype

```

✅ What is __proto__?

`__proto__` is a property available on all JavaScript objects that points to the object’s prototype. This is how JavaScript supports prototype-based inheritance.

It forms the prototype chain, allowing objects to inherit methods and properties from other objects.

```ts
// Base object with shared functionality
const userMethods = {
  login() {
    console.log(`${this.name} logged in.`);
  },
  logout() {
    console.log(`${this.name} logged out.`);
  }
};

// A specific user object
const adminUser = {
  name: "Alice",
  role: "admin"
};

// Inherit userMethods using __proto__
adminUser.__proto__ = userMethods;

// Now adminUser has access to login and logout
adminUser.login();   // Output: Alice logged in.
adminUser.logout();  // Output: Alice logged out.

```

✅ What's Happening Here:

- adminUser doesn't have `login()` or `logout()` methods directly.
- But because we set `adminUser.__proto__ = userMethods`, it inherits those methods.
- When `adminUser.login()` is called, JavaScript looks up the prototype chain and finds it on userMethods.

JavaScript uses prototype-based inheritance, meaning that objects can inherit properties and methods from other objects via the prototype chain.

Every object has an internal `[[Prototype]]` (which can be accessed via __proto__) that points to another object. When you try to access a property or method on an object, JavaScript will look up the prototype chain until it finds it or reaches null.

Functions in JavaScript have a `.prototype` property, which is used when creating new objects with new. This allows all instances to share methods defined on the prototype.

Using new links the new object to the constructor’s `.prototype`, so that the object can inherit methods without storing them directly. This is how prototype inheritance works in practice.

```ts
function Person(name) {
  this.name = name; // this refers to the new object
}

Person.prototype.sayHello = function() {
  console.log("Hello, I'm " + this.name);
};

const user = new Person("Alice");
user.sayHello(); // Hello, I'm Alice

```

- user is created using new Person(...)
- It gets its own name property.
- But the `sayHello` method is not copied to user. Instead, it's found in Person.prototype, which user inherits through __proto__.


For example, if you add a method to Array.prototype, all array instances will inherit that method:

```ts
Array.prototype.sayHello = function() {
  console.log("Hello from array");
};

const arr = [1, 2, 3];
arr.sayHello(); // "Hello from array"

```

You can also set up inheritance between objects using Object.create() or Object.setPrototypeOf(), though direct use of __proto__ is discouraged.

