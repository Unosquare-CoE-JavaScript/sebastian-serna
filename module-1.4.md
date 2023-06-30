# Hardcore functional programming in javascript

# Pure functions

Pure functions are the ones that always return the same result for the same argument and it does not do any side affect like mutating and argument, Using pure functions is convinient they are reliable, reusable and easy to test

```js
// Pure function
const add = (x, y) => x + y;

add(2, 4); // 6
```

```js
let x = 2;

const add = (y) => {
  x += y;
};

add(4); // x === 6 (the first time)
```

The first example returns a value based on the given parameters, regardless of where/when you call it.

If you pass ```2``` and ```4```, you’ll always get ```6```.

Nothing else affects the output.

The second example returns nothing. It relies on shared state to do its job by incrementing a variable outside of its own scope.

This pattern is a developer’s nightmare fuel.

Shared state introduces a time dependency. You get different results depending on when you called the function. The first time results in ```6```, next time is ```10``` and so on.

# Properties, Arguments and Currying

Currying is a technique that allows us to pass arguments to a function through a callable chain, this technique is used in functional programming to create higher order functions

```js
function curry(f) { // curry(f) does the currying transform
  return function(a) {
    return function(b) {
      return f(a, b);
    };
  };
}

// usage
function sum(a, b) {
  return a + b;
}

let curriedSum = curry(sum);

alert( curriedSum(1)(2) ); // 3
```

As you can see, the implementation is straightforward: it’s just two wrappers.

The result of ```curry(func)``` is a wrapper ```function(a)```.
When it is called like ```curriedSum(1)```, the argument is saved in the Lexical Environment, and a new wrapper is returned ```function(b)```.
Then this wrapper is called with ```2``` as an argument, and it passes the call to the original sum.

# Composition

Composition is an operation in which two functions generate a new function, the new function is generated applying a function to the result of another, this is too a higher-order function

```js
const compose = (fn1, fn2) => value => fn2(fn1(value));

const add2AndSquare = compose( add2, square);

// example
const add2        = (n) => n + 2;
const times2      = (n) => n * 2;
const times2add2  = compose(add2, times2);
const add6        = compose(add2, add2, add2);

times2add2(2);  // 6
add2tiems2(2);  // 8
add6(2);        // 8
```

# Functors

Functor is a design pattern inspired by the definition of the categories theory that allows us to apply a new function to values inside a generic type without changing the structure of the generic type

# Monad

Monad is a design pattern to structure the combination of fragments of a function y wrap the returned values in a type with additional computation
