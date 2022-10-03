## Providers
To import and use a class decorated with @Injectable one needs to declare its Type in the providers array of the main application Module.
That makes Angular "know" about the services when instantiating components for your web application.
The concept of providers in Angular goes beyond the collection of classes, in fact, it supports several powerful ways to control how dependency 
injection behaves at **runtime**. Besides strings, the framework supports an object-based notation for defining providers.

### Using a Class
```typescript
{ provide: <key>, useClass: <class> }
```

log.service
```typescript
@Injectable({
  providedIn: 'root'
})
export class LogService {

  constructor() { }

  info(message: string) {
    console.log(`[info] ${message}`);
  }
}
```

custom-log.service
```typescript
import { Injectable } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class CustomLogService {

  constructor() { }

  info(message: string) {
    console.log(`[custom]: [info] ${message}`);
  }

}

```

app.module
```typescript
@NgModule({
  // ...
  
  providers: [
    { provide: LogService, useClass: CustomLogService }
  ],
  //...
})
export class AppModule { }
```
The code above means that all the components that inject the LogService as part of
the constructor parameters are going to receive the CustomLogService implementation at **runtime**.

### Using a Class Factory
Factory provides a possibility to control how the class gets instantiated if you need more than just a default constructor calls. 
```typescript
{ provide: <key>, useFactory: <function> }
```
Let's assume our CustomLog service has a extra filed: `prefix`
```typescript
import { Injectable } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class CustomLogService {

  private prefix = '[custom]';

  setPrefix(value: string) {
    this.prefix = value;
  }

  info(message: string) {
    console.log(`${this.prefix}: [info] ${message}`);
  }

}
```
Next, create an exported function `customLogServiceFactory` that is going to control how the 
CustomLogService instance gets created. In our case we are going to provide a custom prefix like in the example below:
```typescript
export function customLogServiceFactory(userService: Userservice) {
  // const user = new UserService();
  // console.log('UserService', user.id)
  const service = new CustomLogService();
  service.setPrefix('(factory demo)');
  return service;
}
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [...],
  providers: [
    {
      provide: LogService,
      useFactory: customLogServiceFactory,
      deps: [UserService]
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }

```
### Using @Inject Decorator
The `@Inject` decorator instructs Angular that a given parameter must get injected at runtime
src/app/app.module.ts
```typescript

export function dateFactory() {
  return new Date();
}

@NgModule({
  declarations: [...],
  imports: [...],
  providers: [
    ...
    { provide: 'DATE_NOW', useFactory: dateFactory }

  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Now we can use injected value in components
```typescript
import { Component, OnInit, Inject } from '@angular/core';

@Component({
  selector: 'app-user',
  templateUrl: './user.component.html',
  styleUrls: ['./user.component.scss']
})
export class UserComponent implements OnInit {

  constructor(@Inject('DATE_NOW') now: Date) {
    console.log('now', now)
   }

}
```

In same way we can inject service
```typescript
import { Component, OnInit, Inject } from '@angular/core';
import { LogService } from '../services/log.service';

@Component({
  selector: 'app-user',
  templateUrl: './user.component.html',
})
export class UserComponent implements OnInit {

  constructor(@Inject('DATE_NOW') now: Date, @Inject(LogService) logService: LogService) {
    logService.info("In user class")
   }

}
```
Another important use case for the `@Inject` decorator is using **custom types** in TypeScript when the service has different implementation class 
associated with the provider key, like in our early examples with LogService and CustomLogService.

```typescript
import { Component, OnInit, Inject } from '@angular/core';
import { CustomLogService } from '../services/custom-log.service';
import { LogService } from '../services/log.service';

@Component({...})
export class UserComponent implements OnInit {

  constructor(@Inject(LogService) logService: CustomLogService) {
    // here we can call setPrefix function because logService type is CustomLogService
    logService.setPrefix('user-')
    logService.info("In user class")
   }
}

```

### Using a Value
Another scenario for registering providers in the Angular framework is providing instances directly, without custom or default factories.
```typescript
{ provide: <key>, useValue: <value> }
```
There are two main scenarios of providing the values.

- **First scenario** is pretty much similar to the factory functions you can create and
initialize the service instance before other components and services use it.

```typescript
const logService = new CustomLogService();
logService.setPrefix('(factory demo)');

@NgModule({
  // ...  
  providers: [
    { provide: LogService, useValue: logService }
  ],  
  // ...
})
export class AppModule { }
```
- The **second scenario** is related to configuration objects you can pass to initialize or setup other components and services.
```typescript
export interface LoggerConfig {
  logLevel?: string;
  prefix?: string;
}

@NgModule({
  declarations: [],
  imports: [],
  providers: [
      {
        provide: 'logger.config',
        useValue: {
          logLevel: 'info',
          prefix: 'my-logger' // set staic value
        }
      }

  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

custom-log.service
```typescript
import { Injectable, Inject } from '@angular/core';
import { LoggerConfig } from '../app.module';

@Injectable({
  providedIn: 'root'
})
export class CustomLogService {

  private prefix = '[custom]';

  constructor(@Inject('logger.config') config: LoggerConfig){
    console.log(config);
    // read prefix from configs
    this.prefix = config.prefix || '';
  }

  setPrefix(value: string) {
    this.prefix = value;
  }

  info(message: string) {
   // Note: config is not available here
    console.log(`${this.prefix}: [info] ${message}`);
  }

}
```

## Injection Tokens
Angular provides a special generic class `InjectionToken<T>` to help you create custom injection tokens backed by specific types: primitives, classes or interfaces. That enables static type checks and prevents many type-related errors at early stages.

tokens.ts
```typescript
import { InjectionToken } from '@angular/core';
export const REST_API_URL = new InjectionToken<string>('rest.api.url');
```
Now we can use this token within the main application module to register a URL value that all components and services can use when needed:

```typescript
import { REST_API_URL } from './tokens';

@NgModule({
  providers: [
    // ...,    
    { provide: REST_API_URL, useValue: 'http://localhost:4200/api' }
  ]
})
```

user.component.ts
```typescript
import { REST_API_URL } from '../tokens';

@Component({
  selector: 'app-user',
  templateUrl: './user.component.html',
  styleUrls: ['./user.component.scss']
})
export class UserComponent implements OnInit {

  constructor(@Inject(REST_API_URL) apiUrl: string) {
    // Note: injected value available only inside constructor
    console.log(apiUrl)
   }

}
```

