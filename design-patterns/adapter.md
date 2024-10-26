### Adapter pattern
The Adapter Pattern is a structural design pattern that allows incompatible interfaces to work together. It acts as a bridge between two incompatible interfaces
by converting the interface of a class into another interface that a client expects.

### Why Use the Adapter Pattern?

The Adapter Pattern is useful when:

1.  **Integrating Third-Party Code**: You may need to adapt a library or third-party code to work with your existing application.
2.  **Legacy Code**: Allows you to work with older interfaces that don’t match your current requirements.
3.  **Refactoring**: You can adapt existing code without needing to modify the original structure, making your code easier to maintain.

Let’s consider an example where you have an application that calculates area, but it uses a method that expects a square (with a single side parameter). However, a new library you want to integrate uses a rectangle (with width and height).
We’ll create an adapter to bridge this difference.

1. Existing Square Calculator
The existing code expects a Square object.
```ts
// squareCalculator.js
class SquareCalculator {
    calculateArea(square) {
        return square.side * square.side;
    }
}

// The application expects to call `calculateArea` with a `Square` object
const squareCalculator = new SquareCalculator();
const square = { side: 5 };
console.log(squareCalculator.calculateArea(square)); // Output: 25
```

2. New Rectangle Library
The new library represents a rectangle with width and height, which is incompatible with the SquareCalculator.
```ts
// rectangle.js
class Rectangle {
    constructor(width, height) {
        this.width = width;
        this.height = height;
    }

    getArea() {
        return this.width * this.height;
    }
}

const rectangle = new Rectangle(4, 5);
console.log(rectangle.getArea()); // Output: 20
```

3. Adapter Class
The adapter allows a Rectangle to be used with the SquareCalculator by providing a side that the SquareCalculator expects.
```ts
// rectangleToSquareAdapter.js
class RectangleToSquareAdapter {
    constructor(rectangle) {
        this.rectangle = rectangle;
    }

    get side() {
        // Converts the rectangle into a compatible "side" by using an equivalent area calculation
        return Math.sqrt(this.rectangle.width * this.rectangle.height);
    }
}

```
4. Using the Adapter
Now, we can use the adapter to pass a Rectangle object to the SquareCalculator.
```ts
// usage.js
const rectangle = new Rectangle(4, 5);
const adapter = new RectangleToSquareAdapter(rectangle);

console.log(squareCalculator.calculateArea(adapter)); // Output: 20

````

 ### Explanation

-   **SquareCalculator**: The existing calculator expects an object with a `side` property.
-   **Rectangle**: The new library uses `width` and `height` instead.
-   **Adapter**: `RectangleToSquareAdapter` converts the `Rectangle` into a compatible format by providing a `side` property.
