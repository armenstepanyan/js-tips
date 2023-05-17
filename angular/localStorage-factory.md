Safe way to access localStorage
```typescript
import { InjectionToken } from "@angular/core";

export type InjectionTokenType<T extends InjectionToken<any>> =  T extends InjectionToken<infer U> ? U : never;

export const LOCAL_STORAGE = new InjectionToken<Storage>("LocalStorage");

export function localStorageFactory(): InjectionTokenType<typeof LOCAL_STORAGE> {
  if (!isStorageAvailable()) {
    return new MemoryStorage();
  }
  return localStorage;
}

export function isStorageAvailable(): boolean {
  try {
    const storage = window["localStorage"] as Storage;
    const key = "__storage_test__";
    storage.setItem(key, "value");
    storage.removeItem(key);
    return true;
  } catch (e) {
    return false;
  }
}

export class MemoryStorage implements Storage {

  private data: { [index: string]: string } = {};

  get length(): number {
    return Object.keys(this.data).length;
  }

  clear(): void {
    this.data = {};
  }

  getItem(key: string): string | null {
    return key in this.data ? this.data[key] : null;
  }

  key(index: number): string | null {
    const keys = Object.keys(this.data);

    return index >= 0 && keys.length < index ? keys[index] : null;
  }

  removeItem(key: string): void {
    delete this.data[key];
  }

  setItem(key: string, value: string): void {
    this.data[key] = value;
  }
}


```
Provide `LOCAL_STORAGE` in app.module

```typescript
import {
  localStorageFactory,
  LOCAL_STORAGE,
} from "./local-storage/local-storage.factory";

@NgModule({
  declarations: [],
  imports: [],
  providers: [
    {
      provide: LOCAL_STORAGE,
      useFactory: localStorageFactory,
    },
  ],
  bootstrap: [AppComponent],
})
export class AppModule {}

```

Injecting localStorage in service
```typescript
import { InjectionTokenType, LOCAL_STORAGE } from "./local-storage.factory";
import { Inject, Injectable } from "@angular/core";

@Injectable({
  providedIn: "root",
})
export class LocalStorageDataService {
  constructor(@Inject(LOCAL_STORAGE) private _localStorage: InjectionTokenType<typeof LOCAL_STORAGE> ) {}

  get<T>(key: string): T {
    const value = this._localStorage.getItem(key);
    return value ? JSON.parse(value) : null;
  }

  set(key: string, value: any) {
    this._localStorage.setItem(key, JSON.stringify(value));
  }

  remove(key: string) {
    this._localStorage.removeItem(key);
  }
}

```

