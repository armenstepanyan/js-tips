The provided functions are injected at application startup and executed during app initialization. 
If any of these functions returns a Promise or an Observable, initialization does not complete until the Promise is resolved or the Observable is completed

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


