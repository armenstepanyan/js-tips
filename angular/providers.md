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
