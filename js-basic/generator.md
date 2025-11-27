In JavaScript, a **generator** is a special kind of function that can be paused and resumed during execution. This allows you to produce a sequence of values over time,
rather than computing them all at once. Generators are defined using `function*` and use the `yield` keyword to return values.

### ✅ Key Characteristics of Generators

* **1. They can pause execution**
    * A normal function runs from start to end without stopping.
    * A generator can **pause** at each `yield` and **resume** later.

* **2. They return a Generator object**
    * Calling a generator function doesn't run it immediately—it returns an **iterator-like object** (a generator object).

* **3. They work well for sequences and asynchronous patterns**
    * They're often used for:
        * producing **streams of values**
        * implementing **custom iterators**
        * managing **asynchronous flows**

#### 📌 Example of a Generator
```ts
function* numberGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = numberGenerator();

console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: 3, done: false }
console.log(gen.next()); // { value: undefined, done: true }
```
#### 🔍 How it Works

- `yield` pauses the function and returns a value.

- `next()` resumes execution from the last yield.

You can loop through them with for...of:
```ts
for (const n of numberGenerator()) {
  console.log(n);
}
// output
1
2
3
```
#### Example: Infinite Generator

Generators can create infinite sequences without using infinite memory:
```ts
function* infiniteCounter() {
  let i = 0;
  while (true) {
    yield i++;
  }
}

const counter = infiniteCounter();
console.log(counter.next().value); // 0
console.log(counter.next().value); // 1
console.log(counter.next().value); // 2

```

#### Example: Random number generator with internal state
This generator produces an infinite sequence of pseudo-random numbers and remembers the updated seed between calls — without needing a class or external variables.
```ts
function* random(seed) {
  while (true) {
    seed = (seed * 9301 + 49297) % 233280;
    yield seed / 233280;
  }
}

```
Without generators, you'd need a class to store the state.

Usage
```ts
const rand = random(42); // 42 is the seed
console.log(rand.next());
// { value: 0.865..., done: false }

console.log(rand.next());
// { value: 0.231..., done: false }

console.log(rand.next());
// { value: 0.742..., done: false }

```

**Note**: The numbers come from a known formula for a [linear congruential generator (LCG)](https://en.wikipedia.org/wiki/Linear_congruential_generator), one of the oldest and simplest pseudo-random number generator (PRNG) algorithms.

