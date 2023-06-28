# Chapter 1

# What is javascript?

Javascript is an interpreted language with a JIT (Just in time) runtime compilation

Javascript has been a language that has gotten new features overtime however is a language that tries to generate compatibility with older versions. There are many tools that facilitate this work like using transpilers and polyfills, these tools help us to generate javascript code in an older syntax in order to have compatibility en most browsers and environments.

# Chapter 2

# Surveying Javascript

Javascript is a language that has primitive data types asigned by value, these values are mostly known as literals

```js
// primitive data types

let n = 123; // Number
let str = "Hello"; // String double quotes
let str2 = 'Single quotes are ok too'; // String single quotes
let phrase = `can embed another ${str}`; // String backticks
let myBool = true; // Boolean
let myNull = null; // Null value
let myUndefined = undefined; // Undefined value
let mySymbol = Symbol();
```

Every literal represents a value in javascript

Javascript allows us to create objects but in a special way because the language works under the Prototype design pattern. When creating an object the language assigns a special property which is a prototype pointer to an existing object, this allows us to chain properties and methods even though the haven't been defined in the original object and acts as inheritance. We must know that when objects are created in javascript the value is stored in a memory heap and creating a reference to that object.

Javascript also allows to apply many paradigms such as modular programming, object oriented programming and functional programming.

# Chapter 3

# Digging to the Roots of JS

Javascript grants us the concept of closures too, a closure stores and allows access to variables outside its scope mostly known as lexical environment. We could say that the lexical environment refers to the execution context of a function and all variables declared in its scope

```js
function makeCounter() {
  let count = 0;

  return function() {
    return count++;
  };
}

let counter = makeCounter();

alert( counter() ); // 0
alert( counter() ); // 1
alert( counter() ); // 2
```

We can se that the variable count keeps record of the values and whenever we call the returned function it increments the value in the scope of the makeCounter() function

# Chapter 4

# The Bigger Picture

Javascript provides us many features and it is good to know those concepts to be better javascript developers, prevent bugs in our applications, etc
