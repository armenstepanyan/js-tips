# Decorator Pattern

The **Decorator Pattern** is a structural design pattern that allows behavior to be added to individual objects, either statically or dynamically, without affecting the behavior of other objects from the same class. It’s used to extend the functionalities of objects in a flexible and reusable way, without modifying the underlying class.

## Key Concepts:

- **Component Interface**: This defines the interface for objects that can have responsibilities (or behavior) added to them dynamically. The component could be an abstract class or an interface.
  
- **Concrete Component**: This is the core object that the decorator pattern will add behavior to. It implements the component interface and provides default behavior.

- **Decorator**: This is an abstract class or interface that implements the same interface as the component and contains a reference to a component. It forwards requests to the component and may add additional responsibilities before or after forwarding the request.

- **Concrete Decorators**: These are the specific decorators that add behaviors or responsibilities to the component. They extend the functionality of the component by overriding methods and adding additional behavior.

## Key Features:

- **Flexible Enhancements**: You can dynamically add or remove functionalities without altering the structure of the component class.
- **Open/Closed Principle**: It promotes the Open/Closed Principle, allowing objects to be extended without modifying their existing code.
- **Layered Additions**: You can layer multiple decorators, each adding its own behavior on top of the previous one.

## Example:

Imagine you have a basic `Coffee` class, and you want to add features like milk, sugar, or whipped cream to it, but without altering the `Coffee` class itself.

```typescript
// Component interface
interface Coffee {
    cost(): number;
    description(): string;
}

// Concrete Component
class SimpleCoffee implements Coffee {
    cost(): number {
        return 5;
    }
    description(): string {
        return "Simple Coffee";
    }
}

// Decorator
class CoffeeDecorator implements Coffee {
    protected coffee: Coffee;

    constructor(coffee: Coffee) {
        this.coffee = coffee;
    }

    cost(): number {
        return this.coffee.cost();
    }

    description(): string {
        return this.coffee.description();
    }
}

// Concrete Decorators
class MilkDecorator extends CoffeeDecorator {
    cost(): number {
        return this.coffee.cost() + 1;
    }
    description(): string {
        return this.coffee.description() + ", Milk";
    }
}

class SugarDecorator extends CoffeeDecorator {
    cost(): number {
        return this.coffee.cost() + 0.5;
    }
    description(): string {
        return this.coffee.description() + ", Sugar";
    }
}

// Usage
const coffee = new SimpleCoffee();
console.log(coffee.description() + " $" + coffee.cost());

const milkCoffee = new MilkDecorator(coffee);
console.log(milkCoffee.description() + " $" + milkCoffee.cost());

const milkSugarCoffee = new SugarDecorator(milkCoffee);
console.log(milkSugarCoffee.description() + " $" + milkSugarCoffee.cost());
```

### When to Use the Decorator Pattern:
- When you need to add responsibilities to individual objects without affecting other objects.
- When extending the functionality of a class through inheritance is not feasible or results in too many subclasses.
- When you want to be able to remove added functionality dynamically.

### @decorator
In TypeScript (ES6), the @decorator syntax can be used to decorate classes, methods, properties, and even parameters. 
Here's how you can use the Decorator Pattern with the `@decoratorName` syntax.
 
 #### 1. Enabling Decorators in tsconfig.json

Create **tsconfig.json** file
```
tsc --init
```

Note: if `typescript` is not installed run `npm install -g typescript` comand to install it globally 
 
 ```ts
{
  "compilerOptions": {
    "target": "ES6",             // or higher
    "experimentalDecorators": true,  // Enable the decorator syntax
    "emitDecoratorMetadata": true,   // (Optional) Enable if you need metadata reflection
    "module": "commonjs"         // You can use other module systems like "esnext"
  }
}
```
#### 2. Installing Required Packages
In order to transpile and run your TypeScript code with decorators, ensure you have TypeScript installed.
You can install TypeScript and other necessary packages via npm if not already installed:

Generate package.json file: (`-y` for default metadata)
```
npm init -y
```

```
npm install typescript --save-dev
```

#### 3. Simple Example of Using @decorator
```ts
// A simple property decorator that logs when a property is accessed or set
function LogProperty(target: any, propertyKey: string | symbol) {
    let value: any;
  
    const getter = () => {
      console.log(`Get: ${String(propertyKey)} => ${value}`);
      return value;
    };
  
    const setter = (newVal: any) => {
      console.log(`Set: ${String(propertyKey)} => ${newVal}`);
      value = newVal;
    };
  
    Object.defineProperty(target, propertyKey, {
      get: getter,
      set: setter,
      enumerable: true,
      configurable: true,
    });
  }
  
  class Person {
    @LogProperty
    public name: string;
  
    constructor(name: string) {
      this.name = name;
    }
  }
  
  // Example usage
  const person = new Person("Alice");
  person.name = "Bob"; // This will trigger the setter and log: Set: name => Bob
  console.log(person.name); // This will trigger the getter and log: Get: name => Bob
  
```

Output
```
Set: name => Alice
Set: name => Bob
Get: name => Bob

```

### Summary of Requirements in tsconfig.json:
**"experimentalDecorators": true:** Enables the use of the @decorator syntax.
**"emitDecoratorMetadata": true:** (Optional) If you need metadata reflection, especially useful for frameworks like Angular.

#### 4. Compile and Run
If you just run `tsc`, it should automatically pick up the tsconfig.json from the root of your project directory. However, if it's not picking it up automatically, you can explicitly tell the TypeScript compiler to use it:

```
tsc -p tsconfig.json

```
The -p flag stands for "project", and it instructs the TypeScript compiler to use the tsconfig.json file from the specified path.

If you want to compile a specific file (instead of all files in the project), you can specify the file name after the tsc command. For example:
```
{
  "compilerOptions": {
  // ...
  },
  "include": [
    "index.ts"
  ]
 }

```

Run compiled  code
```
node main.js

```
