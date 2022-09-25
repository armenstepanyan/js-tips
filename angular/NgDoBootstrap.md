### NgDoBootstrap
Hook for manual bootstrapping of the application instead of using `bootstrap` array in 
**@NgModule** annotation. This hook is invoked only when the bootstrap array is empty or not provided
```typescript
interface DoBootstrap {
  ngDoBootstrap(appRef: ApplicationRef): void
}
```

app.module.ts
```typescript
import { ApplicationRef, DoBootstrap, NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppComponent as ProAppComponent } from './pro/app.component';
import { BasicComponent } from './basic/basic.component';
import { HttpClient, HttpClientModule } from '@angular/common/http';

@NgModule({
  declarations: [
    ProAppComponent,
    BasicComponent
  ],
  imports: [BrowserModule, HttpClientModule],
})
export class AppModule implements DoBootstrap {

  constructor(private http: HttpClient) {}

  ngDoBootstrap(appRef: ApplicationRef) {
    this.http.get<{ active: boolean }>(`http://localhost:5001/get-license`).subscribe(
      (data) => {
        const APP = data.active ? ProAppComponent : BasicComponent;
        appRef.bootstrap(APP, document.getElementById('app'))
      }
    )
  }
}
```

index.html
```html
  <div id="app"></div>
```
