### 1. What is @Injectable in Angular and what happens if we mark a service class with or without @Injectable?
In Angular, `@Injectable` is a decorator used to indicate that a class can be injected as a dependency into other classes using Angular's dependency injection system. It is especially relevant for service classes, which often act as shared resources or provide business logic and data management.

#### Purpose:

The `@Injectable` decorator is used to make a class available for dependency injection. It allows Angular to instantiate the class, resolve its dependencies, and inject it wherever needed.

#### Usage:
You typically use`@Injectable` on service classes that you want to provide and inject into other components, directives, or services.

#### Providing Dependencies:
By marking a class with`@Injectable`, you can specify whether the class should be provided in a specific module, component, or the root injector (providedIn: 'root').

#### What Happens When You Mark a Service Class with @Injectable
- **Dependency Injection:**
Angular can inject the service into other classes (components, directives, etc.) because the @Injectable decorator registers the class with the Angular dependency injection system.

- **Resolving Dependencies:**
If the service itself depends on other services or resources, Angular can resolve those dependencies for it.

- **Tree-Shakable Providers:**
If `providedIn: 'root'` is specified in the `@Injectable` decorator, Angular ensures that the service is tree-shakable. It will only be included in the compiled JavaScript bundle if it's actually used in the application.

- **Scoped Providers:**
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

### 2. What is the OnPush Change Detection Strategy?
ChangeDetectionStrategy.OnPush tells Angular to run change detection for a component only when it has a strong signal that the component’s data might have changed.
This makes the component more predictable and performant.
#### ▶ How it works

With OnPush, Angular triggers change detection only when one of the following happens:

1. **An @Input() reference changes**
Angular compares the previous value and the new value using reference equality (===).

  - Primitives → value comparison
  - Objects/arrays → reference comparison

2. **An event occurs inside the component**
(e.g., button click, input type, etc.)

3. **An observable emits inside the template**
(e.g., using `async` pipe)

4. **Change detection is manually triggered**
via `ChangeDetectorRef.markForCheck()` or `detectChanges()`.

It drastically reduces unnecessary change detection runs and makes components faster, especially in large apps.

Angular’s OnPush strategy is used to optimize change detection.
With the default strategy, Angular runs change detection for a component every time any data changes anywhere in the application.

With `OnPush`, local component state **still updates normally** as long as the update occurs inside an Angular event (like `(click`)), or through an observable in the template. Angular will run the component’s change detection in those cases.

You only need to manually trigger change detection when the update happens outside Angular’s zone — for example, in a native event listener, a third-party callback, or setTimeout run outside Angular.

### 3. What are Feature, Lazy, Shared Modules in Angular, and how do you create them?
Feature modules are Angular modules used to organize application functionality into cohesive, focused sections.They help organize the codebase, improve maintainability, and allow features to be lazy loaded.

To create a feature module, you:

- Generate a new Angular module.
- Declare components, pipes, and services inside it.
- Optionally create a routing module for feature-specific routes.
- Import the feature module into AppModule (eager loading) or configure lazy loading in the app routing.

**Lazy-loaded** modules are modules that Angular loads only when the route is accessed. Feature modules help structure the app; lazy loading is a performance strategy. A feature module may or may not be lazy-loaded.

  
**A Shared Module** is used to re-use common components, directives, pipes, and sometimes services across multiple feature modules.
▶ Key Characteristics

1. Contains reusable components, directives, pipes
Example: CustomButtonComponent, DateFormatterPipe, TooltipDirective.
2. Imported by feature modules, not AppModule
This avoids cluttering the root module and keeps things modular.
3. Should not provide singleton services
Services in shared modules may create multiple instances if imported in multiple modules. Singleton services belong in Core Module.
4. No routing
Shared modules are purely for reusable pieces — they don’t define routes.
