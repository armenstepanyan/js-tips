### Dependency Injection
Dependency Injection (DI) is a design pattern that allows a program to follow the Dependency Inversion Principle,
where high-level modules do not depend on low-level modules. Instead, both depend on abstractions. This makes the system more modular, easier to test, and maintainable.

In JavaScript, Dependency Injection can be implemented in various ways, such as through constructor injection, method injection, or property injection. 
Here’s a simple example using constructor injection:

#### Example: Dependency Injection in JavaScript
Let’s consider a scenario where we have a `UserService` that depends on a `UserRepository`. Instead of creating an instance of UserRepository directly within UserService, we inject it as a dependency.
```ts
// UserRepository.js
class UserRepository {
    getUser(id) {
        // Simulate fetching user data from a database
        return { id, name: 'John Doe' };
    }
}

// UserService.js
class UserService {
    constructor(userRepository) {
        this.userRepository = userRepository;
    }

    getUser(id) {
        return this.userRepository.getUser(id);
    }
}

// main.js
const userRepository = new UserRepository();
const userService = new UserService(userRepository);

const user = userService.getUser(1);
console.log(user); // Output: { id: 1, name: 'John Doe' }

```

#### Explanation
- **UserRepository**: This class handles the data access logic. In a real application, it would interact with a database or an API.

- **UserService**: This class uses the UserRepository to perform operations related to users. Notice that it doesn't create a UserRepository instance directly. Instead, it accepts one through its constructor.

- **Dependency Injection**: In main.js, we create an instance of UserRepository and pass it to UserService. This decouples the classes, making UserService easier to test and maintain.

#### Benefits of Dependency Injection
- Decoupling: Classes are less dependent on concrete implementations, which makes them more flexible and easier to refactor.

- Testability: You can easily replace dependencies with mocks or stubs when testing.

- Configuration: Dependencies can be configured outside of the classes themselves, allowing for easier management of different configurations (e.g., for different environments).

#### Example 2: using Base class
Let's create an example that demonstrates Dependency Injection with an abstract base class.
We'll have an interface (abstract class) for data storage, and two implementations: one for saving data to a file system and another for saving data to a database. 
Finally, we'll use Dependency Injection in the User class to choose between these storage methods.

Step 1: Define the Abstract Base Class
```ts
// Storage.js
class Storage {
    save(user) {
        throw new Error('Method "save()" must be implemented.');
    }
}

```

Step 2: Implement Concrete Classes
```ts
// FileStorage.js
const fs = require('fs');
const path = require('path');

class FileStorage extends Storage {
    save(user) {
        const filePath = path.join(__dirname, 'users.json');
        const userData = JSON.stringify(user);
        fs.writeFileSync(filePath, userData);
        console.log(`User saved to file: ${filePath}`);
    }
}
```

#### DatabaseStorage
```ts
class DatabaseStorage extends Storage {
    save(user) {
        // Simulate saving to a database
        console.log(`User saved to database: ${JSON.stringify(user)}`);
    }
}
```

Step 3: Create the User Class

```ts
class User {
    constructor(storage) {
        this.storage = storage; // Dependency is injected
    }

    saveUserData(userData) {
        this.storage.save(userData);
    }
}
```

Step 4: Using the Classes with Dependency Injection
```ts
// main.js
const FileStorage = require('./FileStorage');
const DatabaseStorage = require('./DatabaseStorage');
const User = require('./User');

// Choose the storage method
const useFileStorage = true; // Change to false to use DatabaseStorage

let storage;
if (useFileStorage) {
    storage = new FileStorage();
} else {
    storage = new DatabaseStorage();
}

const user = new User(storage);
user.saveUserData({ id: 1, name: 'John Doe' });

```

#### Explanation
Abstract Base Class (Storage): This class defines an interface with a save method that must be implemented by any concrete storage class.

Concrete Classes:

- FileStorage: Implements the save method to save user data to a file.
- DatabaseStorage: Implements the save method to simulate saving user data to a database.
- User Class: This class depends on the Storage abstract class. The actual storage implementation is injected into the User class via the constructor.

**Main Logic**: In main.js, you can choose which storage implementation to use by setting the useFileStorage variable. This decision is made outside the User class, allowing for flexibility.
