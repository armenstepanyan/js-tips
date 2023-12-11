### Angular 17 & Signals example

#### Generate standalone users component
```console
ng generate component --standalone users
```
User component
```typescript
import { Component, signal } from '@angular/core';
import { CommonModule } from '@angular/common';

interface User {
  id: number;
  name: string;
}

@Component({
  selector: 'app-users',
  standalone: true,
  imports: [CommonModule],
  templateUrl: 'user.component.html',
})
export class UserComponent {

  initialUsers: User[] = [
    { id: 1, name: 'Leanne Graham'},
    { id: 2, name: 'Ervin Howell'},
    { id: 3, name: 'Chelsey Dietrich'},
    { id: 4, name: 'Kurtis Weissnat'},
    { id: 5, name: 'John Doe'},
  ];

  users = signal(this.initialUsers); 
}

```
View
```html
<h1>Users</h1>
  @if(users()){
    <ul>
      @for(user of users(); track user.id){
        <li>
          {{ user.id }}. {{ user.name }}
        </li>
      }
    </ul>
  }
```
