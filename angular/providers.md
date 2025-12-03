When a component declares a dependency, Angular tries to satisfy that dependency with its own `ElementInjector`. If the component's injector lacks the provider, it passes the request up to its parent component's `ElementInjector`.

The requests keep **forwarding up** until Angular finds an injector that can handle the request or runs out of ancestor `ElementInjector` hierarchies.

If Angular doesn't find the provider in any ElementInjector hierarchies, it goes back to the element where the request originated and looks in the **ModuleInjector** hierarchy. If Angular still doesn't find the provider, it throws an error.

### Resolution modifiers
Resolution modifiers fall into three categories:
- What to do if Angular doesn't find what you're looking for, that is `@Optional()`
- Where to start looking, that is `@SkipSelf()`
- Where to stop looking, `@Host()` and `@Self()`

By default, Angular always starts at the current `Injector` and keeps searching all the way up. Modifiers allow you to **change** the starting, or self, location and the ending location.

### @Optional()
`@Optional()` allows Angular to consider a service you inject to be optional. This way, if it can't be resolved at runtime, Angular resolves the service as `null`, rather than throwing an error. 

### @Self()
Use `@Self()` so that Angular will only look at the `ElementInjector` for the current component or directive.
A good use case for @Self() is to inject a service but only if it is available on the current host element. To avoid errors in this situation, combine `@Self()` with `@Optional()`

### SkipSelf()
Angular starts its search for a service in the parent `ElementInjector`, rather than in the current one.

### @Host
`@Host()` lets you designate a component as the last stop in the injector tree when searching for providers. Even if there is a service instance further up the tree, Angular won't continue looking 

1. If we add service to `providers` array for each componet/directive, service instance should be created for each component

user.component
```typescript
import { Component, OnInit } from '@angular/core';
import { LogService } from './services/log.service';

@Component({
  selector: 'app-user',
  template: `
   <p appHighlight>user works!</p>
   <ng-content></ng-content>
  `,
  providers: [LogService]  // <----
})
export class UserComponent implements OnInit {

  constructor(private logService: LogService) { }

  ngOnInit(): void {
    this.logService.print('Hello')
  }

}
```

appHighlight.directive
```typescript
import {Directive, ElementRef, Renderer2, Host, Self, SkipSelf} from '@angular/core';
import { LogService } from '@app/services/log.service';

@Directive({
  selector: '[appHighlight]',
  providers: [LogService]  // <---
})
export class HighlightDirective {

  constructor(private elementRef: ElementRef,
    private renderer: Renderer2,
    private logService: LogService
    ) {
      this.logService.print('Directive works');
      this.renderer.setStyle(this.elementRef.nativeElement, "background-color", "orange");
   }
}

```

log.service
```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class LogService {

  constructor() {
    console.log('[LogService] created');
  }

  print(val: any) {
    console.log('[Value is]: ', val)
  }
}
```

Output
```console
[LogService] created
[LogService] created
```
2. Note: if we use `SkipeSelf()` in directive with `providers` array, instance of LogService will not be created
```typescript
import { Directive, ElementRef, Renderer2, Host, Self, SkipSelf } from '@angular/core';
import { LogService } from '@app/services/log.service';

@Directive({
  selector: '[appHighlight]',
  providers: [LogService] // <--- provide service here
})
export class HighlightDirective {

  constructor(private elementRef: ElementRef,
    private renderer: Renderer2,
    @SkipSelf() private logService: LogService  // <-- no new instance
  ) {...}
}

```

3. If we have component projected inside `app-user` with providers array, new instance should be created again 
```html
<app-user>
  <h2>Hello</h2>
  <app-person></app-person>
</app-user>
```
person.component
```typescript
import { Component, OnInit, Self, SkipSelf } from '@angular/core';
import { LogService } from '@app/services/log.service';

@Component({
  selector: 'app-person',
  providers: [LogService]  // <----- create new instance
})
export class PersonComponent implements OnInit {

  constructor(private logService: LogService) { }

  ngOnInit(): void {
    this.logService.print("Person works")
  }

}
```
### Replacing service with useExisting
We can replace and use another service for our user component
```typescript
@Component({
  selector: 'app-user',
  templateUrl: './user.component.html',
  // using providers array here
  providers: [
    {
      provide: LogService,
      useExisting: ColorLogService
    }
  ]
})
export class UserComponent implements OnInit {

  constructor(private logService: LogService) { }

  ngOnInit(): void {
    this.logService.print('Hello') // <-- should be called colorService.print() method
  }
}

```

**Note** if we use `providers` array, all childs (directives, projected components) will use replaced intance.

Output
```console
[ColorLogService] created
[ColorLogService]: Directive works
[ColorLogService]: User works 
[ColorLogService]: Person works 
```
Now if we change `providers`  to `viewProviders` the instance of replaced service will be available only in component view and directives that are
inside view (not in projected content)

```typescript
@Component({
  selector: 'app-user',
  templateUrl: './user.component.html',
  // using viewProviders
  viewProviders: [
    {
      provide: LogService,
      useExisting: ColorLogService
    }
  ]
})
export class UserComponent implements OnInit {

  constructor(private logService: LogService) { }

  ngOnInit(): void {
    this.logService.print('User works')
  }
}
```

Output
```console
[ColorLogService] created
[LogService] created

[ColorLogService]: Directive works 
[ColorLogService]: User works 
[Value is]:  Person works  // person is projected so it use LogService print
```
### 1. useClass
`useClass` tells Angular: “When someone asks for this token, create a new instance of this class.”

### 2. useExisting
`useExisting` tells Angular: “When someone asks for this token, give them the already existing instance of another token.”

### 3. useFactory
`useFactory` tells Angular: “When someone asks for this token, run this factory function to create the instance, and provide the result.”
So instead of Angular automatically instantiating a class (useClass) or aliasing (useExisting), you have **full control** over how the object is created.

```ts
function createEngine() {
  return {
    start: () => console.log('Engine started via factory')
  };
}

@NgModule({
  providers: [
    { provide: 'Engine', useFactory: createEngine }
  ]
})
export class AppModule {}

```

- Here, when Angular injects 'Engine', it will call createEngine() and return that object.
- You can return any object, not just classes.

