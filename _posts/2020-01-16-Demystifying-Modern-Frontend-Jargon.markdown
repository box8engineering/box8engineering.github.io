---
layout: post
title:  "Demystifying Modern Frontend Jargon"
date:   2020-01-16 23:22:43 +0530
categories: jekyll update
author: Ananya Neogi
permalink: ananya-neogi/demystifying-modern-frontend-jargon/
---

The world of frontend programming is full of confusing jargons and we see a lot these terms getting thrown around. This is my small attempt to break down some of these terms or concepts into simpler terms with very limited to no code examples.

## 1. Pure function
A function is called pure when its return value is determined only by its own arguments. When given the same argument, the result will always be the same.

```js
function twice(num) {
  return num * 2;
}
```
If we pass `4` to the this function, it will always return `8`. 

**Key points:**
- Pure functions provide [referential transparency](https://en.wikipedia.org/wiki/Referential_transparency). This means that when a pure function is invoked it can be replaced by its own result. We can replace `twice(4)` by `8` without affecting our outcome.
- Pure functions cannot reference variables from their outer scope. If any function is doing so then its not a pure function.

```js
// NOT a pure function

let num = 8; // can be mutated from anywhere else

function impureTwice() {
  return num * 2;
}
```
- Pure functions cannot call impure functions.
- Pure functions do not produce side effects (see below).

-------------

## 2. Impure function
An impure function mutates state outside its scope. Any function that has side effects is impure.

```js
let count = 0; // can be mutated from anywhere else
function increment() {
  return ++count;
}

// alerts and dialogs also cause side effect
function showAlert() {
  alert('oh hey! this is a side effect!');
}

// Beware! might seem like a pure function, but its not
function getRandomRange(min, max) {
  return Math.random() * (max - min) + min;
}
```
-------------

## 3. Side effect
<blockquote>An operation, function or expression is said to have a side effect if it modifies some state variable value outside its local environment.</blockquote>

When a function or expression modifies state outside its own context, the result is a side effect. For example, manipulating the DOM, making an API call, modifying database, creating alerts and dialogs, etc.

-------------

## 4. Stateful vs Stateless

Stateful programs or components store data in memory about the current state. They can modify the state and access its history as well.

Stateless programs or components depend only on their arguments and do not access or even need knowledge about anything outside their scope. They do not reference any information from earlier in their execution. **Pure functions are stateless.** Its the statelessness that enables referential transparency in pure functions.

-------------

## 5. Imperative vs Declarative Paradigms
JavaScript can support both programming paradigms.
Imperative code gives instructions informing the computer **how** to achieve a desired result, such as `for` loop.
Declarative code tells the computer **what** you want to achieve rather than how, and the computer takes care of how to achieve the end result, such as `Array.map`.

-------------

## 6. Reactive Programming & Observables

**Reactive programming** is the idea that you can create entire program just by defining the different streams and the operations that are performed on those streams.
*Streams* are nothing but sequence of values over time.
<blockquote>
Here's an analogy: Think about reactive programming as plumbing. We decide which pipes we need in our application, we decide how those pipes are connected together and then we turn on the water and sit back.
</blockquote>

**Observables** are a blueprint for how we want to create streams, subscribe to them, react to new emitted values, and combine streams together to build new ones. JavaScript observables are an implementation of the observer pattern.

There are two types of observables -
- *Cold observables:* They begin pushing only when we subscribe to it. If we subscribe again, it will start over.
- *Hot observables:* They will always push even if we're not specifically reacting to them with a subscription.

[RxMarbles](https://rxmarbles.com/) is a great resource to visualize all how the operations happen.

-------------

## 7. Tree shaking
Tree shaking is a term used in reference to JavaScript module bundling. It refers to the static analysis of all imported code and exclusion of anything that isn't used.
The concept behind tree shaking is *live code inclusion*. This means that we include the parts that are needed to begin with, as opposed to removing the parts that are not needed at the later stage.

-------------

## 8. Compilation: JIT vs AOT
**Just-In-time (JIT)** compilation is the process of translating code written in a programming language to machine code at runtime (during a program or application's execution). At runtime, certain dynamic information is available, such as type identification. A JIT compiler monitors to detect functions or loops of code that are run multiple times. These pieces of code are then compiled. If they're quite commonly executed, JIT will optimize them and also store the optimized, compiled code for execution. Browsers use JIT compilation to run JavaScript. 

**Ahead-Of-Time (AOT)** compilation is the process of translating code written in a programming language to machine code before execution (as opposed to at runtime). Doing so reduces runtime overhead and compiles all files together rather than separately.

**Some benefits of AOT:**
- Better security because evaluation is already done.
- Templates and styles are inlined with JS so fewer asynchronous calls.
- Small download size, because the compiler need not to be download as the app is precompiled.
- AOT also enables tree shaking.

-------------

## 9. Reducer
If you've used Redux or functional programming, chances are you might already know this very well. But if we put aside that, what does a reducer mean itself? In simple terms, a reducer provides instructions for combining things. 
Typically a reducer function takes an accumulator, which is the on-going result of our combinations as we move along and the list of things that we wish to combine.
Here's a (not very practical 😂) example of making cookie- 

```js
reduce(mix, ["flour", "butter", "sugar", "eggs"]) // result ➡️ ["cookie dough"]
```
We *combined* our list of ingredients to our desired result! 🍪 
Here, `mix` is our accumulator containing the on-going result of our combination so far. So, if we add something else to our list of ingredients our accumulator will change.

```js
reduce(mix, ["flour", "butter", "sugar", "eggs", "chocolate chip"]) // result ➡️ ["chocolate chip cookie dough"]
reduce(mix, ["flour", "butter", "sugar", "eggs", "blueberry"]) // result ➡️ ["blueberry cookie dough"]

```
As we can see, we have **reduced** our ingredients into a fully formed cookie dough!

## 10. Thunk
In computer programming, a thunk is just another word for a function. But this is a special name for a function that’s returned by another function and it takes no arguments.
Thunks are used to delay or defer a computation until its result is needed, or to insert operations at the beginning or end of the other computations. Then when that thunk is invoked, it performs a potentially expensive computation and/or causes some side effect to occur.

For example -
Suppose you wish to do some mathematical operations on numbers. Now, creating a would thunk mean not to do the computations directly, but instead create a function with no arguments that will calculate it when the actual value is needed.

```js

function add(x, y) {
    // this is a thunk because it defers work for later
    return function() { // it can be named, or anonymous
        return x + y; 
    };
}

const thunk = add(1,2); // we can store the returned function to a variable
// OR use it directly like ➡️ add(1,2)()

// we can pass the thunk to different places
checkIfEven(thunk);

// we can use it in other calculations
const twice = thunk() * 2;
```

Here, `thunk()` will return `3` wherever used unless we change its value by calling `add` again with different arguments.

---------