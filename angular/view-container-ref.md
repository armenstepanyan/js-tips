### Dynamic Component Loading
Dynamic Component Loading allows you to load and display a component at runtime, without having to declare it in the` @NgModule` or template directly. This is useful for scenarios where the components need to be loaded dynamically based on user actions, configuration, or when you need to show components like modals, popups, or dynamic forms that aren't known at compile time.

Angular provides `ViewContainerRef` and `ComponentFactoryResolver` to load components dynamically into the DOM.

- **ViewContainerRef**: This is a reference to a container where the component will be dynamically inserted. It can be used to add or remove components from the view.

- **ComponentFactoryResolver**: This is used to create a factory for a given component. A factory allows us to create a component dynamically.

- **ng-template**: This is the placeholder where the dynamic component will be loaded.


#### Simple Usage Example
**1. Create a Component to be Loaded Dynamically**
```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-dynamic',
  template: `<p>This is a dynamically loaded component!</p>`
})
export class DynamicComponent {}
```

**2. Create a Host Component to Load the Dynamic Component**

Next, we create a component where the dynamic loading will occur. This component will contain the logic to load the DynamicComponent


```ts
import { Component, ViewChild, ViewContainerRef, ComponentFactoryResolver } from '@angular/core';
import { DynamicComponent } from './dynamic.component';

@Component({
  selector: 'app-dynamic-loader',
  template: `
    <button (click)="loadComponent()">Load Dynamic Component</button>
    <ng-template #dynamicContainer></ng-template>
  `
})
export class DynamicLoaderComponent {
  @ViewChild('dynamicContainer', { read: ViewContainerRef }) container!: ViewContainerRef;

  constructor(private componentFactoryResolver: ComponentFactoryResolver) {}

  loadComponent() {
    // Clear any previously loaded components
    this.container.clear();

    // Create a factory for the dynamic component
    const componentFactory = this.componentFactoryResolver.resolveComponentFactory(DynamicComponent);

    // Dynamically create the component
    const componentRef = this.container.createComponent(componentFactory);
  }
}
```

#### Explanation:

- `@ViewChild('dynamicContainer', { read: ViewContainerRef })`: This decorator gives us access to the ng-template reference in the template.

- `this.container.clear()`: Clears any previously inserted components from the container.

- `this.componentFactoryResolver.resolveComponentFactory(DynamicComponent)`: Resolves the component's factory, which can be used to dynamically create an instance of the component.

- `this.container.createComponent(componentFactory)`: Dynamically creates and inserts the component into the container.

**3. Add the Dynamic Component to the Module**
Make sure that the DynamicComponent is declared in the AppModule (or any module you're using), as it needs to be compiled ahead of time.

```ts
@Component({
  selector: 'app-root',
  standalone: true,
  imports: [DynamicLoaderComponent, DynamicComponent],
  template: `
    <h1>Hello from {{ name }}!</h1>
    <app-dynamic-loader></app-dynamic-loader>
    
  `,
})
export class App {
  name = 'Angular';
}

```

