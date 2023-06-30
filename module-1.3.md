# Understanding Asynchronous code

El código síncrono se ejecuta línea por línea, esto significa que el motor debe esperar a que una expresión finalice para continuar a la siguiente, por ende generando un bloqueo. El código asíncrono nos permite evitar esta casuística.

Synchronous code is executed line by line, this means that the engine must wait for an expression to end to continue to the next one thus making a blocking. Asynchronous code allows us to avoid this.

```js
// Synchronous code
document.write('Hi'); // First
document.write('<br>');

document.write('Mayukh') ;// Second
document.write('<br>');
  
document.write('How are you'); // Third

// Asynchronous code
document.write('Hi');
document.write('<br>');

setTimeout(() => {
    document.write('Let us see what happens');
}, 2000);

document.write('<br>');
document.write('End');
document.write('<br>');
```

# Event Loop

Javascript is a single threaded language and the way it allows us to write asynchronous code is through the event loop. The event loop is an API that's provided by the environments in which javascrip is executed, we can describe the event loop as a data structure that stores function call stacks implementing a first in/first out pattern making a queue for the processes to be executed

```js
console.log('Hi!');

setTimeout(() => {
    console.log('Execute immediately.');
}, 0);

console.log('Bye!');

```
In this example, the timeout is 0 second, so the message ```'Execute immediately.'``` should appear before the message ```'Bye!'```. However, it doesn’t work like that.

The JavaScript engine places the following function call on the callback queue and executes it when the call stack is empty. In other words, the JavaScript engine executes it after the ```console.log('Bye!').```

# Callbacks

Usually we know callsbacks as functions that are being passed as an argument from a function A to a function B in order for the callback to be executed after other block code has been executed

```js
function myCallbackCaller(mockArgument, myCallbackFn) {
  console.log(mockArgument)
  myCallbackFn();
}

myCallbackCaller('Executed before the callback', function() {
  console.log('I am executed after my caller');
});

// Logs 'Executed before the callback'
// Logs 'I am executed after my caller'
```

# Promesas

Promises are a javascript object that represents the initizialitation or failure of an operation, when the state of this operation concludes the promise will return a value, we can capture that value with the methods provided by the Promise prototype object

```js
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('foo');
  }, 300);
});

myPromise
  .then(handleFulfilledA, handleRejectedA)
  .then(handleFulfilledB, handleRejectedB)
  .then(handleFulfilledC, handleRejectedC);
```

# Async/Await

These are reserved javascript keywords that help us to simplify promises, it allows us to catch the returned value from a promise in a synchronous way

```js
// a promise
let promise = new Promise(function (resolve, reject) {
    setTimeout(function () {
    resolve('Promise resolved')}, 4000); 
});

// async function
async function asyncFunc() {

    // wait until the promise resolves 
    let result = await promise; 

    console.log(result);
    console.log('hello');
}

// calling the async function
asyncFunc();
```

# Generators

Generators are objects produced by a generator function, these value are provided on demand with the ```.next()``` method which helps us to manage asynchronous code in a controlled way

```js
function* generateSequence() {
  yield 1;
  yield 2;
  return 3;
}

let generator = generateSequence();

let one = generator.next();

alert(JSON.stringify(one)); // {value: 1, done: false}
```

These are the fetures that javascript provides us to write asynchronous code
