### Symbol.iterator

Call iterator of string type (String.prototype has own iterator)
```
const rangeIterator = '0123456789'[Symbol.iterator]();
console.log(rangeIterator.next()); // {value: "0", done: false}
console.log(rangeIterator.next()); // {value: "1", done: false}
console.log(rangeIterator.next()); // {value: "2", done: false}
...
console.log(rangeIterator.next()); // {value: "9", done: false}
console.log(rangeIterator.next()); // {done: true}
```

### Create range iterator
```
let range = {
  from: 1,
  to: 5
};

// 1. call to for..of initially calls this
range[Symbol.iterator] = function() {

  // ...it returns the iterator object:
  // 2. Onward, for..of works only with this iterator, asking it for next values
  return {
    current: this.from,
    last: this.to,

    // 3. next() is called on each iteration by the for..of loop
    next() {
      // 4. it should return the value as an object {done:.., value :...}
      if (this.current <= this.last) {
        return { done: false, value: this.current++ };
      } else {
        return { done: true };
      }
    }
  };
};

// now it works!
for (let num of range) {
  alert(num); // 1, then 2, 3, 4, 5
}

```
- The range itself does not have the next() method.
- Instead, another object, a so-called “iterator” is created by the call to `range[Symbol.iterator]()`, and its next() generates values for the iteration.


```
var myIterable = {}
myIterable[Symbol.iterator] = function* () {
    yield 1;
    yield 2;
    yield 3;
};
[...myIterable] // [1, 2, 3]
```

Technically, we may merge them and use range itself as the iterator to make the code simpler. Like this:

```
let range = {
  from: 1,
  to: 5,

  [Symbol.iterator]() {
    this.current = this.from;
    return this;
  },

  next() {
    if (this.current <= this.to) {
      return { done: false, value: this.current++ };
    } else {
      return { done: true };
    }
  }
};

for (let num of range) {
  alert(num); // 1, then 2, 3, 4, 5
}
```


### Iterator for class
```
class Route {

 constructor(stations) {
   this.stations = stations;
 }

  get(idx) {
   return this.stations[idx];
 }

 [Symbol.iterator]() {
   return new RouteIterator(this);
 }
}

class RouteIterator {

 constructor(route) {
  this._route = route;
  this._nextIdx = 0;
 }

 next() {
  if (this._nextIdx === this._route.stations.length) {
   return { done: true }
  }

  const result = {
   value: this._route.get(this._nextIdx),
   done: false
  }

  this._nextIdx++;

  return result;
 }
}

const route = new Route(['Moscow', 'New York', 'London']);


for (let item of route) {
 console.log(item);
}
```

Output

```
"Moscow" 
"New York" 
"London" 

```

[JSbin example](https://jsbin.com/xagisik/2/edit?js,console)

