### Input Signal example

Simple version with passing data with `@Input` decorator


app.component.ts
```typescript
export class AppComponent {
  users: User[] = [
    { id: 1, name: 'Michael', lastName: 'Scott', username: 'michael.scott' },
    { id: 2, name: 'Dwight', lastName: 'Schrute', username: 'dwight.schrute' },
    { id: 3, name: 'Angela', lastName: 'Martin', username: 'angela.martin' },
    { id: 4, name: 'Jim', lastName: 'Halpert', username: 'jim.halpert' },
  ];

  addUser() {
    this.users = [
      {
        id: 5,
        name: 'Andy',
        lastName: 'Bernard',
        username: 'andy.bernard',
      },
      ...this.users,
    ];
    console.log('Current Users', this.users);
  }
}
```


```html
<section class="header">
  <button (click)="addUser()">Add Andy</button>
</section>
<app-user [users]="users" ></app-user>

```

User component
```typescript
@Component({
  selector: 'app-user',
  standalone: true,
  template: `
    <input (input)="updateQuery($event)" placeholder="Start typing..." />
    <ul>
        @for (user of filteredUsers(); track user.id) {
        <li>{{ user.name }}</li>
        }
    </ul>
  `
})
export class UserComponent {

 @Input() users: User[] = [];
 
 private query = signal('');

  updateQuery(e: Event) {
    this.query.set((e.target as HTMLInputElement).value);
  }

  protected filteredUsers = computed(() => this.users.filter(({ name }) =>  {
    return  name.startsWith(this.query())
  })
);
  
}
```

Now after adding a new user the `users` list will be updated but new user will not be shown in the list. Because the `users` input is not reactive. And changes of that input is ignored by `computed` function.

### Solution
We can transform users input to `singnal`

```typescript
export class UserComponent {
    // using setter
  @Input({ alias: 'users' }) set _users(users: User[]) {
    this.users.set(users);
  }

  private users = signal<User[]>([]);

  private query = signal('');

  updateQuery(e: Event) {
    this.query.set((e.target as HTMLInputElement).value);
  }

  protected filteredUsers = computed(() =>
    this.users().filter(({ name }) => {
      return name.startsWith(this.query());
    })
  );
}
```

Starting with angular 17.1 we can simplify this code to this:
```typescript
import { Component,  computed, input, signal } from '@angular/core';

@Component({
  selector: 'app-user',
  standalone: true,
  imports: [],
  templateUrl: './user.component.html',
  styleUrl: './user.component.scss',
})
export class UserComponent {
  
  // no need to use `@Input()` decorator
  users = input<User[]>([]);

  private query = signal('');

  updateQuery(e: Event) {
    this.query.set((e.target as HTMLInputElement).value);
  }

  protected filteredUsers = computed(() =>
    this.users().filter(({ name }) => {
      return name.startsWith(this.query());
    })
  );
}
```

Using `alias` and `transform`

```typescript
  usersList = input<User[], User[]>([], {
    alias: 'users',
    transform: toUpperCase
  });

function toUpperCase(users: User[]): User[] {
  return users.map((user) => {
    return {
      ...user,
      name: user.name.toUpperCase(),
    };
  });
}

```
