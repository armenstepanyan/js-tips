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

### Angular Example
1. Define the Logging Service Interface
In Angular, we typically use an abstract class as an interface for services.
```ts
// logging.service.ts
export abstract class LoggingService {
  abstract log(message: string): void;
}
```

2. Concrete Implementations
Create concrete classes that implement the LoggingService interface.
```ts
// console-logging.service.ts
import { Injectable } from '@angular/core';
import { LoggingService } from './logging.service';

@Injectable()
export class ConsoleLoggingService extends LoggingService {
  log(message: string): void {
    console.log(`Console Log: ${message}`);
  }
}

// server-logging.service.ts
import { Injectable } from '@angular/core';
import { LoggingService } from './logging.service';

@Injectable()
export class ServerLoggingService extends LoggingService {
  log(message: string): void {
    // Imagine this sends the log message to a remote server
    console.log(`Server Log: ${message}`);
  }
}
```
3. Factory Function
Create a factory function that will determine which logging service to use.
```ts
// logging.factory.ts
import { ConsoleLoggingService } from './console-logging.service';
import { ServerLoggingService } from './server-logging.service';
import { LoggingService } from './logging.service';

export function loggingFactory(environment: any): LoggingService {
  return environment.production
    ? new ServerLoggingService()
    : new ConsoleLoggingService();
}
```

4. Use the Factory in a Provider
In your module, use the factory function in a provider to determine which logging service to use at runtime.
```ts
// app.module.ts
import { LoggingService } from './logging.service';
import { loggingFactory } from './logging.factory';

@NgModule({
  declarations: [AppComponent],
  imports: [],
  providers: [
    {
      provide: LoggingService,
      useFactory: () => loggingFactory(environment),
    },
  ],
  bootstrap: [],
})
export class AppModule {}
```
