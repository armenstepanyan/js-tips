The provided functions are injected at application startup and executed during app initialization. 
If any of these functions returns a Promise or an Observable, initialization does not complete until the Promise is resolved or the Observable is completed.

```typescript
import { APP_INITIALIZER, Injectable } from '@angular/core';

@Injectable()
export class AppConfig {
  private readonly config: any;

  constructor() {
    // Initialize your config values here, for example by fetching them from an API.
    this.config = {
      apiUrl: 'https://api.example.com'
    };
  }

  getConfig() {
    return this.config;
  }
}

export function initializeApp(appConfig: AppConfig) {
  return () => appConfig.getConfig();
}

@NgModule({
  providers: [
    AppConfig,
    {
      provide: APP_INITIALIZER,
      useFactory: initializeApp,
      deps: [AppConfig],
      multi: true
    }
  ],
  // ...
})
export class AppModule { }

```

1. We have a service called AppConfig which provides some configuration values (in this case, just a base API URL).
2. We define a function initializeApp that takes an instance of AppConfig and returns a function that, when executed, will call appConfig.getConfig(). This function is used as a factory for the `APP_INITIALIZER`.
3. In the providers array of the module, we provide AppConfig, and we use the `APP_INITIALIZER` token to specify that we want to run the initializeApp function before the application starts. We also specify that it depends on the AppConfig service.
4. The `multi: true` flag allows for multiple functions to be registered as initializers. They will be executed in the order in which they are provided.

This allows you to perform any necessary setup before your Angular application starts, ensuring that it has the required configuration in place.


```typescript
function initializeApp(): Promise<any> {
  return new Promise((resolve, reject) => {
    // Do some asynchronous stuff
    resolve();
  });
}

@NgModule({
 imports: [BrowserModule],
 declarations: [AppComponent],
 bootstrap: [AppComponent],
 providers: [{
   provide: APP_INITIALIZER,
   useFactory: () => initializeApp,
   multi: true
  }]
 })
export class AppModule {}
```

Provide deps 

```typescript
import { MyService } from '@app/core/services';
import { myInitializerFactory } from './my-initializer.factory';

@NgModule({
  ...
  providers: [
    {
      provide: APP_INITIALIZER,
      useFactory: myInitializerFactory,
      deps: [WINDOW, MyService],
      multi: true,
    }
  ],
  bootstrap: [AppComponent],
})
export class AppModule {}

```
my-initializer.factory.ts
```typescript
import { isPlatformServer } from '@angular/common';
import { PLATFORM_ID } from '@angular/core';

import { InjectionTokenType } from '../utility-types';

import { WINDOW } from './window.factory';
import { ShopifyService } from '@app/core/services/shopify.service';

export function shopifyInitializerFactory(
  // access provided deps here
  window: InjectionTokenType<typeof WINDOW>,
  shopifyService: ShopifyService,
) {
   myService.init(window.location.href);  
  return () => {};
}

```

my-service.ts
```typescript

@Injectable({
  providedIn: 'root',
})
export class MyService {
  public param = '';

  public constructor() {

  }

  init(href: string) {
    const url = new URL(href);
    this.param = url.searchParams.get('app');        
  }
}


```


