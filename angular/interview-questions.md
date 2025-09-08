### 1. What is @Injectable in Angular and what happens if we mark a service class with or without @Injectable?
In Angular, `@Injectable` is a decorator used to indicate that a class can be injected as a dependency into other classes using Angular's dependency injection system. It is especially relevant for service classes, which often act as shared resources or provide business logic and data management.

1. Key Points About @Injectable
Purpose:
The @Injectable decorator is used to make a class available for dependency injection. It allows Angular to instantiate the class, resolve its dependencies, and inject it wherever needed.

2. Usage:
You typically use @Injectable on service classes that you want to provide and inject into other components, directives, or services.

3. Providing Dependencies:
By marking a class with @Injectable, you can specify whether the class should be provided in a specific module, component, or the root injector (providedIn: 'root').

#### What Happens When You Mark a Service Class with @Injectable
- Dependency Injection:
Angular can inject the service into other classes (components, directives, etc.) because the @Injectable decorator registers the class with the Angular dependency injection system.

- Resolving Dependencies:
If the service itself depends on other services or resources, Angular can resolve those dependencies for it.

- Tree-Shakable Providers:
If providedIn: 'root' is specified in the @Injectable decorator, Angular ensures that the service is tree-shakable. It will only be included in the compiled JavaScript bundle if it's actually used in the application.

- Scoped Providers:
You can also specify providedIn to limit the scope of the service to a specific module or lazily loaded feature module.

#### What Happens If You Don't Use @Injectable
1. Dependency Injection Fails:

Without the `@Injectable` decorator, Angular's dependency injection system cannot inject the service into other classes. This will result in a runtime error if you attempt to inject the service.

For example:

```ts
export class MyService {
  constructor() {}
}

// Error: No provider for MyService!
@Component({
  selector: 'app-example',
  template: '<h1>Example Component</h1>'
})
export class ExampleComponent {
  constructor(private myService: MyService) {}
}
```
2. Manual Instantiation:

If you don't use `@Injectable`, you would need to manually instantiate the service class wherever you need it, which defeats the purpose of Angular's dependency injection system.

3. Dependency Resolution Issues:
If the service class has its own dependencies (e.g., it depends on another service), Angular won't be able to resolve them.

#### @Injectable with Dependencies
If a service has dependencies, marking it with @Injectable ensures that Angular can inject those dependencies into the service class. 
For example:

```ts
@Injectable({
  providedIn: 'root'
})
export class MyService {
  constructor(private http: HttpClient) {}
}
```

Without `@Injectable`, Angular wouldn't know how to inject HttpClient into MyService, and you'd get a runtime error.

#### When @Injectable Is Not Required
If a service class doesn't have any dependencies (doesn't use the constructor for dependency injection), 
Angular can still provide it without @Injectable. However, it's a best practice to always use @Injectable for consistency and future-proofing. This way, if you later add dependencies to the service, you won't run into issues.
