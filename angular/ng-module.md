## Creating lazy load module
Lazy loading is the technique where angular loads the Modules only on a need basis rather than all at once. It is also called `on-demand` loading. By default, Angular Loads the modules eagerly. Lazy Loading of Angular Modules reduces the initial load time of the app. We use the loadChilden method of the Angular Router to lazy load them when the user navigates to a route.
Create module with routing

``` ng g m dashboard --routing ```

Create component

``` ng g c dashboard ```


### dashboard-routing
```typescript
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { DashboardComponent } from './dashboard.component';


const routes: Routes = [
  {
    path: '',
    component: DashboardComponent
  },
  {
    path: 'item1', // route relative to dashboard
    component: itemComponent,
  },
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class DashboardRoutingModule { }

```

Add dashboard-routing to dashboard.module
```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

import { DashboardRoutingModule } from './dashboard-routing.module';
import { DashboardComponent } from './dashboard.component';


@NgModule({
  declarations: [DashboardComponent],
  imports: [
    CommonModule,
    DashboardRoutingModule,
  ]
})
export class DashboardModule { }

```

Then add in main route
```typescript
  {
    path: 'dashboard',
    loadChildren: () => import('./dashboard/dashboard.module').then(m => m.DashboardModule),
  },
  ```

Create subroute relative to dashboard,  dashboard/item1
```typescript
{ path: 'item1', } // without '/'
```

### Submodule router-outlet
admin.html
```html
<ul>
  <li>
    <a [routerLink]="['/admin', 'user-list']">User list</a>
  </li>
  <li>
    <a [routerLink]="['/admin', 'contact']">Contact</a>
  </li>
</ul>
<router-outlet></router-outlet>
```

admin-routing.ts
```typescript
import { AdminComponent } from "@app/admin/admin.component";

const routes: Routes = [
  {
    path: '',
    component: AdminComponent,
    children: [
      {
        path: '',
        pathMatch: 'full',
        redirectTo: 'user-list',
      },
      {
        path: 'user-list',
        component: UserListComponent,
      },
      {
        path: 'contact',
        component: ContactComponent,
      },
    ],
  },

];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule],
})
export class AdminRoutingModule {}
```

### Create canLoad guard
Generate guard under `guards` folder
```
ng g guards/admin
```

src\app\guard\admin.guard.ts
```typescript
import { Injectable } from '@angular/core';
import { CanLoad, Route, UrlSegment, ActivatedRouteSnapshot, RouterStateSnapshot, UrlTree } from '@angular/router';
import { Observable } from 'rxjs';
import { UserService } from '../service/user.service';

@Injectable({
  providedIn: 'root'
})
export class AdminGuard implements CanLoad {
  constructor(private userService: UserService){

  }
  canLoad(
    route: Route,
    segments: UrlSegment[]): Observable<boolean> | Promise<boolean> | boolean {

    console.log(this.userService.admin$.value);
    return this.userService.admin$.value;
  }
}
```
Admin modulae will be loaded only if user is admin
Create `user.service`
```
ng g s service/user
```

```typescript
import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class UserService {

  public admin$ = new BehaviorSubject<boolean>(false);
  constructor() { }
}

```

Create checkbox to toggle isAdmin
app.component.html
```html
<h2>
  Main app
</h2>

<ul>
  <li>
    <a [routerLink]="['admin']">Admin</a>
  </li>
  <li>
    <a [routerLink]="['profile']">Profile</a>
  </li>
</ul>

<p>
  <label>
    <input type="checkbox" [checked]="isChecked" (click)="handleChange()"> Admin
  </label>
</p>

<router-outlet></router-outlet>
```

app.component.ts
```typescript
import { Component, OnInit } from '@angular/core';
import { UserService } from './service/user.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent implements OnInit {
  title = 'ng-canload-test';
  public isChecked = false;

  constructor(private userService: UserService){

  }

  ngOnInit(){
    this.userService.admin$.subscribe(val => {
      this.isChecked = val;
    })
  }

  handleChange(){
    this.userService.admin$.next(!this.isChecked)
  }

}
```

admin-routing.module.ts
```typescript
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { AdminGuard } from './guard/admin.guard';


const routes: Routes = [
  {
    path: 'profile',
    loadChildren: () => import('./profile/profile.module').then(m => m.ProfileModule)
  },
  {
    path: 'admin',
    loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule),
    canLoad: [AdminGuard] // <-- add guard here
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```
