## Preloading Strategy in Angular
Preloading in Angular means loading the Lazy loaded Modules in the background asynchronously, 
while the user is interacting with the app. This will help boost up the loading time of the app.

How to Enable Preloading

To make use of Preloading, first, we need to enable lazy loading of the Modules. Mark the modules with the, when you define routes as shown below. The angular will lazy load those modules.


### preloadingStrategy types:


- PreloadAllModules
This strategy will preload all the lazy loaded modules.
- NoPreloading
This will disable all the preloading. This is default behaviour i.e. if you do not specify the preloadingStrategy, then the angular assumes you do not want to preload.
- Custom preloader

**CustomPreloadingStrategy.ts**

```typescript
import { Injectable } from "@angular/core";
import { Observable, of, timer } from "rxjs";
import { flatMap, map } from "rxjs/operators";
import { PreloadingStrategy, Route } from "@angular/router";

@Injectable()
export class CustomPreloadingStrategy implements PreloadingStrategy {

  preload(route: Route, loadRoute: () => Observable<any>): Observable<any> {
    if (route.data && route.data.preload) {

      const delay = route.data.delay ? route.data.delay : 0;
      console.log("preload called on " + route.path + " delay is " + delay);
      return timer(delay).pipe(map(() => {
        console.log("load route", route.path);
        return loadRoute()
      }))

    } else {
      console.log("no preload for the path " + route.path);
      return of(null);
    }

  }

}

```

**src\app\app-routing.module.ts**

```typescript
import { NgModule } from '@angular/core';
import { Routes, RouterModule, PreloadAllModules, NoPreloading } from '@angular/router';
import { CustomPreloadingStrategy } from './custom-preloader';


const routes: Routes = [
  {
    path: 'dashboard',
    loadChildren: () => import('./dashboard/dashboard.module').then(m => m.DashboardModule),
    data: { preload: true, delay: 2000 }
  },
  {
    path: 'admin',
    loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule),
    data: { preload: true, delay: 3000 }
  }
];

@NgModule({
  imports: [
    RouterModule.forRoot(routes, { 
	preloadingStrategy: CustomPreloadingStrategy 
	})
  ],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```
