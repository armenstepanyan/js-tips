Without scrict mode
```
function f1(){
  console.log(this)
}

f1();   // Window
f1.call(null);    // Window
f1.call(undefined);   // Window
f1.call('');    // String {''}
f1.call(true);    // Boolean {true}
f1.call(1);   // Number {1}
f1.call({});    // {}
```

With strict mode
```
function f1(){
  'use strict'; 
  console.log(this)
}

f1();     //undefined
f1.call(null);    // null
f1.call(undefined);   // undefined
f1.call('');    // ''
f1.call(true);    // true
f1.call(1);     // 1
f1.call({});     // {}
```
