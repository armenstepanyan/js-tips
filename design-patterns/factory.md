### Factory pattern
The Factory Design Pattern is a creational design pattern used to 
create objects without specifying the exact class of the object that will be created. It provides a way to encapsulate the instantiation logic, making the code more modular, flexible, and easier to maintain

#### Why Use the Factory Pattern?
- Loose Coupling: The client code (the code using the objects) doesn't need to know the specifics of object creation.
- Flexibility: Easy to introduce new classes or products without altering the client code.
- Simplified Code: Encapsulates object creation logic, making code cleaner and easier to understand.

#### Basic Structure of the Factory Pattern
Here’s how the pattern is generally structured:

- Product Interface or Abstract Class: Defines the common interface for the products that the factory will create.
- Concrete Product Classes: Implement the Product interface, providing specific implementations.
- Factory Class: Contains a method that decides which product class to instantiate and returns a Product type.

 ```ts
// Step 1: Product Interface (simulated through method naming convention)
// Step 2: Concrete Product Classes

class SMSNotification {
    send(message) {
        console.log(`Sending SMS: ${message}`);
    }
}

class EmailNotification {
    send(message) {
        console.log(`Sending Email: ${message}`);
    }
}

// Step 3: Factory Class

class NotificationFactory {
    static createNotification(type) {
        if (type === "SMS") {
            return new SMSNotification();
        } else if (type === "Email") {
            return new EmailNotification();
        } else {
            throw new Error(`Unknown notification type: ${type}`);
        }
    }
}

const notification = NotificationFactory.createNotification("SMS");
notification.send("Hello there!");

const emailNotification = NotificationFactory.createNotification("Email");
emailNotification.send("Hello via Email!");
 ```
### Explanation

1.  **Concrete Product Classes (`SMSNotification` and `EmailNotification`)**: Each has a `send` method, implementing the functionality for each notification type.
2.  **Factory Class (`NotificationFactory`)**: The `createNotification` method instantiates the appropriate class based on the `type` argument.
3.  **Usage**: You can create and use notifications by specifying the type without needing to know about the internal classes.

### Advantages of This Pattern in JavaScript

-   **Code Reusability**: All object creation logic is in one place, making it easier to reuse and extend.
-   **Centralized Creation**: It simplifies object creation, especially when the instantiation process is complex.

This design pattern is particularly helpful in larger JavaScript applications where managing dependencies between classes is key.

