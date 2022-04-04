```
import { HttpClient } from '@angular/common/http';

@Injectable({ providedIn: 'root' })
export class UserService {
  constructor(
    private http: HttpClient,
    @Inject(ENVIRONMENT) private env: InjectionTokenType<typeof ENVIRONMENT>,
  ) {}
  
  public changeEmail(email: { email: string }) {
    return this.http.put(`${this.env.apiUrl}/change-email`, email);
  }
  
  public send() {
    return this.http.post(`${this.env.apiUrl}/send/deletion`, {});
  }
  
  public changeUserName(userName: { fullName: string }) {
    return this.http.put(`${this.env.apiUrl}/user/change/names`, userName).pipe(
      tap(res => {
        this.myStore.update(({ profile }) => {
          const newProfile = { ...profile, fullName: userName.fullName };
          const nickName = `${userName.fullName}`;

          return {
            profile: newProfile,
          };
        });
      }),
    );
  }
  
}
  
```

### switchMap
```
this.activatedRouter.params
  .pipe(
    switchMap(({ token }) => {
      return this.checkToken(token);
    }),
    takeUntil(this.destroy$),
  )
  .subscribe({
    next: resp => {
      if (resp && resp.success) {        
        this.router.navigate(['/dashboard']);
      }
    },
    error: err => {
      // show error
    },
  });
  
  checkToken(token: string) {
    return this.http.post<TokenResponse>(`${this.env.apiUrl}/confirm/email`, { token });
  }
```
### combineLatest
```
combineLatest([this.fields$, this.steps$])
  .pipe(
    skip(1),
    debounceTime(500),
    switchMap(([updatedFields, updatedSteps]) => {
       ...
       this.sendData(...)
    }).subscribe(...)
```


