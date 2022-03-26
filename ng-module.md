## Creating lazy load module
Lazy loading is the technique where angular loads the Modules only on a need basis rather than all at once. It is also called `on-demand` loading. By default, Angular Loads the modules eagerly. Lazy Loading of Angular Modules reduces the initial load time of the app. We use the loadChilden method of the Angular Router to lazy load them when the user navigates to a route.
Create module with routing

``` ng g m dashboard --routing ```

Create component

``` ng g c dashboard ```


### dashboard-routing
```
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
```
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
```
  {
    path: 'dashboard',
    loadChildren: () => import('./dashboard/dashboard.module').then(m => m.DashboardModule),
  },
  ```

Create subroute relative to dashboard,  dashboard/item1
```
{ path: 'item1', } // without '/'
```

### Submodule router-outlet
admin.html
```
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

admin-routing
```
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
