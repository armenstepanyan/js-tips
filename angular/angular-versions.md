## Angular updates

### Version 14

- Standalone components
  Standalone components provide a simplified way to build Angular applications. Standalone components, directives, and pipes aim to streamline the authoring experience by reducing the need for NgModules

- Typed Angular Forms
  Typed forms ensure that the values inside of form controls, groups, and arrays are type safe across the entire API surface. This enables safer forms, especially for deeply nested complex cases.
  [Typed Forms](https://github.com/armenstepanyan/js-tips/blob/master/angular/ng-forms.md#typed-forms)

- Route.title

```typescript
const routes: Routes = [{
  path: 'home',
  component: HomeComponent
  title: 'My App - Home'  // <-- Page title
}, {
  path: 'about',
  component: AboutComponent,
  title: 'My App - About Me'  // <-- Page title
}];
```
- Optional injectors in Embedded Views
v14 adds support for passing in an optional injector when creating an embedded view through `ViewContainerRef.createEmbeddedView` and `TemplateRef.createEmbeddedView`. The injector allows for the dependency injection behavior to be customized within the specific template.

```typescript
viewContainer.createEmbeddedView(templateRef, context, {
  injector: injector,
})
```

- NgOptimizedImage
The `NgOptimizedImage` directive in Angular is responsible for implementing performance best practices when loading images.
```html
 <img
    [rawSrc]="img.url"
    width="4000"
    height="3000" />
```

### CanMatch
- canMatch
The `CanMatch` guard is a new feature that was introduced in Angular **v14.2**. It will activate the route and load the lazy-loaded component if all guards return true, otherwise it will navigate to the next route with the same name.

```typescript
export const APP_ROUTES: [
  {
    path: 'dashboard',
    canMatch: [() => canMatch(['ADMIN'])], // route will be activated if function returns true
    loadComponent: () => import('./dashboard/admin.component'),
  },
  {
    path: 'dashboard',
    canMatch: [() => canMatch(['MANAGER'])],
    loadComponent: () => import('./dashboard/manager.component'),
  },
  {
    path: 'dashboard',
    loadComponent: () => import('./dashboard/everyone.component'),
  }
]
```

Example 2
```typescript
const routes: Routes = [
  { path: '', component: HomeComponent },
  {
    path: 'dashboard',
    loadComponent: () => import('./admin-dashboard/admin-dashboard.component').then(c => c.AdminDashboardComponent),
    canMatch: [(route: Route, segments: UrlSegment[]) => {
      const router = inject(Router);
      return inject(UserPermissionsService).isAdmin$.pipe(
        map(isAdmin => isAdmin || router.createUrlTree(['']))
      );
    }]
  },

  {
    path: 'dashboard',
    component: UserDashboardComponent,
  },

  { path: '**', component: NotFoundComponent },
];

```

UserPermissionsService
```typescript
import { Injectable } from '@angular/core';
import { of } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class UserPermissionsService {

  isAdmin$ = of(false);

  constructor() { }
}
```

### Angular v15
- Standalone components are stable
- The `NgOptimizedImage` directive is stable
