### Understanding Angular

### Decorator
The **decorator** pattern is a design pattern that allows behavior to be added to an individual object, dynamically, without affecting the behavior of other objects from the same class.
[Read More](https://github.com/armenstepanyan/js-tips/blob/master/angular/decorator.md)

### Preloading Strategy
In Angular, the **Preloading Strategy** is a mechanism that allows you to load certain modules in the background (preloading) while the user is interacting with the application. This helps improve the user experience by reducing the time it takes to navigate to different parts of the application. [Read More](https://github.com/armenstepanyan/js-tips/blob/master/angular/preloading-strategy.md)


### NgDoBootstrap
If we want to manually bootstrap (start) an Angular application. [Example](https://github.com/armenstepanyan/js-tips/blob/master/angular/NgDoBootstrap.md)

### Feature modules
Feature modules are NgModules for the purpose of organizing code [Link](https://angular.io/guide/feature-modules).
A feature module delivers a cohesive set of functionality focused on a specific application need such as a user workflow, routing, or forms

### APP_INITIALIZER
In Angular, `APP_INITIALIZER` is a token used in the dependency injection system to provide a way to **run some code before the application is initialized**. It's often used for tasks like fetching initial configuration, loading data, or performing any other setup that needs to be done before the application starts.

The APP_INITIALIZER is typically used in the providers array of an Angular module. It takes an array of functions that return a Promise. These functions will be executed in the specified order, and the application will not start until all promises are resolved.

[Example](https://github.com/armenstepanyan/js-tips/blob/master/angular/app-initializer.md)

### OnPush strategy
If you want to reduce change detection checking on component, use OnPush strategy. [OnPush](https://angular.io/guide/change-detection-skipping-subtrees) change detection instructs Angular to run change detection for a component subtree only when:

1. The root component of the subtree receives new inputs as the result of a template binding. Angular compares the current and past value of the input with `==`
2. Angular handles an event (for example using event binding, output binding, or @HostListener ) in the subtree's root component or any of its children whether they are using OnPush change detection or not.

[Example](https://github.com/armenstepanyan/js-tips/blob/master/angular/change-detection.md)


### Directives
If you want to change the appearance or behavior of DOM elements and Angular components use attribute directives
[Directive example](https://github.com/armenstepanyan/js-tips/blob/master/angular/ng-directive.md).

If you want to dynamically change attributes use `HostBinding`. 
`@HostBinding` is a decorator in Angular that allows you to set the properties of a host element (the element on which a directive is applied). This is often used to dynamically bind properties based on certain conditions.

```typescript
export class AppComponent {
@Directive({
  selector: '[appCustomClassDirective]'
})
export class CustomClassDirective {
  @Input() set appCustomClassDirective(condition: boolean) {
    this.hasSpecialClass = condition;
  }

  @HostBinding('class.special') hasSpecialClass: boolean;
}
}
```

View
```html
<div [appCustomClassDirective]="true">
  This div will have the special class if condition is true!
</div>

```

[Example of HostBinding](https://github.com/armenstepanyan/js-tips/blob/master/angular/ng-directive.md#host-property)

### NgTemplateOutlet
`NgTemplateOutlet` is a structural directive in Angular that allows you to conditionally render a template. It gives you the ability to choose which template to render dynamically at runtime.
```typescript
@Component({
  selector: 'app-example',
  template: `
    <ng-container *ngTemplateOutlet="template; context: user1"></ng-container>

    <ng-container *ngTemplateOutlet="template; context: user2"></ng-container>

    <ng-template #template let-name="name">
      <p>Hello, {{ name }}!</p>
    </ng-template>
  `
})
export class ExampleComponent {
  user1 = { name: 'John Doe' };
  user2 = { name: 'Agent Smith' };
}

```
[Example](https://github.com/armenstepanyan/js-tips/blob/master/angular/ng-ngtemplateoutlet.md)

### NG_VALUE_ACCESSOR
**NG_VALUE_ACCESSOR** is a special token in Angular that is used to provide a bridge between Angular's forms API and native DOM elements. It's a part of the Control Value Accessor pattern, which allows Angular forms to communicate with custom form controls
[Example](https://github.com/armenstepanyan/js-tips/blob/master/angular/ng-value-accessor.md)

```typescript
const MY_CUSTOM_INPUT_CONTROL_ACCESSOR = {
  provide: NG_VALUE_ACCESSOR,
  useExisting: forwardRef(() => MyCustomInputComponent),
  multi: true,
};

@Component({
  selector: 'app-my-custom-input',
  template: `
    <input [(ngModel)]="value">
  `,
  providers: [MY_CUSTOM_INPUT_CONTROL_ACCESSOR],
})
export class MyCustomInputComponent implements ControlValueAccessor {
  value: any;
  
  // Implement methods for NG_VALUE_ACCESSOR interface
  writeValue(value: any) {
    this.value = value;
  }

  registerOnChange(fn: any) {
    this.onChange = fn;
  }

  registerOnTouched(fn: any) {
    this.onTouched = fn;
  }
}

```

### ViewChild & ContentChild
ViewChild is an Angular decorator that allows a component to access a child component, directive, or element in its template.
```typescript
@Component({
  selector: 'app-parent',
  template: `
    <app-child></app-child>
  `
})
export class ParentComponent {
  @ViewChild(ChildComponent) childComponent: ChildComponent;

  ngAfterViewInit() {
    // Now, you can access and interact with childComponent
    this.childComponent.doSomething();
  }
}
```
**ContentChild** lets you access elements or components that are passed into a component as part of its content from the parent component.
```typescript

@Component({
  selector: 'app-child',
  template: `
    <ng-content></ng-content>
  `
})
export class ChildComponent implements AfterContentInit {
  @ContentChild('contentElement') contentElement: ElementRef;

  ngAfterContentInit() {
    // The content element is now accessible after content initialization
    console.log(this.contentElement.nativeElement.textContent);
  }
}

@Component({
  selector: 'app-parent',
  template: `
    <app-child>
      <div #contentElement>
        This is projected content. // <-- projected content
      </div>
    </app-child>
  `
})
export class ParentComponent {}
```

[Example](https://github.com/armenstepanyan/js-tips/blob/master/angular/view-child.md)

### Pipes
In Angular, a pipe is a feature that allows you to transform or format data before displaying it in the template.
[Pipe example](https://github.com/armenstepanyan/js-tips/blob/master/angular/ng-pipes.md)


### Guards
 Guards that can be used to control the navigation flow in an Angular application.

 - CanActivate
 Determines if a route can be activated. It's used to prevent a user from navigating to a specific route if certain conditions are not met.

 - CanActivateChild
 Determines if a route can be deactivated. It's used to prompt the user for confirmation before leaving a page, for example, in unsaved form scenarios

 - CanLoad
 Determines if a lazy-loaded module can be loaded. It prevents the application from loading a module until specific conditions are met.

 - Resolve
 Used to pre-fetch data before a component is activated, ensuring that the required data is available before the component is displayed.

 - CanMatch (from angular 14)
 A guard determines if a route can be matched. If all guards return true, navigation continues, and the Router uses the route. If any guard returns false, the route is skipped for matching, and other route configurations are processed instead. In other words, it allows you to control whether a specific route can be activated based on certain conditions. [CanMatch example](https://github.com/armenstepanyan/js-tips/blob/master/angular/angular-versions.md#canmatch)

 [Guard example](https://github.com/armenstepanyan/js-tips/blob/master/angular/ng-module.md#create-canload-guard)

### Resolution modifiers
 Angular dependency injection resolution mechanism of the service starts from component and stops either when a service is found or `NUllInjector` is reached. [Angular link](https://angular.io/guide/hierarchical-dependency-injection)

 [Example](https://github.com/armenstepanyan/js-tips/blob/master/angular/providers.md#resolution-modifiers)

 ### Injection tokens
 An injection token in Angular is a string or object that acts as a unique identifier for a dependency when it is registered in the Angular dependency injection system. It's used to look up and retrieve dependencies from the injector

 ```typescript
import { InjectionToken } from '@angular/core';

export const MY_INJECTION_TOKEN = new InjectionToken<string>('my-injection-token'); 
 ```
Usage in Providers:
```typescript
import { MY_INJECTION_TOKEN } from './my-injection-token';

@NgModule({
  providers: [
    { provide: MY_INJECTION_TOKEN, useValue: 'my-config-value' }
  ]
})
export class MyModule { }
```
[Example](https://github.com/armenstepanyan/js-tips/blob/master/angular/injection-token.md)

### Signals
Angular Signals is a system that granularly tracks how and where your state is used throughout an application, allowing the framework to optimize rendering updates.
[Signal example](https://github.com/armenstepanyan/js-tips/blob/master/angular/angular-17-signal.md)

[Signal vs RxJs](https://github.com/armenstepanyan/js-tips/blob/master/angular/signal-rxjs.md)
