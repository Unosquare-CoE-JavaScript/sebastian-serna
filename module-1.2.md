# Scope and Closures

# Chapter 1: What's the scope?

Javascript is known as a script language and it is assumed that the execution of its programs are processed in a single read but in reality the language has a parsing/compilation phase that moves all variable declarations and function declarations to the top of the script which is a key concept when we talk about scope, this process is known as hoisting

```js
console.log(myHoistedVar); //Logs 'I am hoisted'
myHoistedFunction(); // Logs 'I am a function and I am hoisted'

var myHoistedVar = 'I am hoisted';

function myHoistedFunction() {
  console.log('I am a function and I am hoisted');
}
```

As you can see even though we're calling the variable and function before they are even defined javascript still logs their values because in the parsing/compilation phase those were moved to the top of the script and were assigned a value making them available before definition.

```js
console.log(myHoistedVar); // Reference Error: myHositedVar is not defined
console.log(myHoistedConst); // Reference Error: myHoistedConst is not defined

let myHoistedVar = 'I am hoisted';
const myHoistedConst = 'I am a hoisted const'
```
What happens when we declare variables with let and const?. Variables defined with let and const are hoisted to the top of the block, but not initialized, meaning: The block of code is aware of the variable, but it cannot be used until it has been declared. Using a let variable before it is declared will result in a ReferenceError. The variable is in a "temporal dead zone" from the start of the block until it is declared.

# Chapter 2: Illustrating Lexical Scope

Variables declared with var have a global scope and variables defined with let and const have a block scope, in the execution of a script javascript creates lexical environments/bubbles in which the declarations live. When the code executes in a read javascript asks in which lexical environments it is currently and will try to search the declaration of the variable, it will use the closest declaration to determine which value will be read.

```js
function primaryFunc() {
  // myLocalVariable is declared in this scope
  const myLocalVariable = 'I am a local varible with  block scope';
  function nestedFunction() {
    // javascript tries to search if myLocalVariable is declared in this scope, doesn't find it goes to the nearest lexical environment
    console.log(myLocalVariable);
  }
  nestedFunction(); // Logs 'I am a local varible with  block scope'
}

primaryFunc(); 
```

# Chapter 3: The Scope Chain

As previously mentioned lexical environments are created for each block forming a scope chain and determines the path in whcich the variables will be accesible, this chain does reads from in an in/out pattern which applies to arrow functions as well.

# Chapter 4: Around the Global Scope

Dependiendo de los ambientes en que se este ejecutando javascript el motor se encarga de asignar este scope global, en web y navegadores las declaraciones del script se almacenan en el objeto Window, existen los web workers que son plataformas en un navegador y actuan como APIS, Node trata cada archivo como su propio module y el scope pertenece a cada modulo

Depending of the environment in which javascript is running the engine assigns the global scope, in web and browsers the declarations of the script are stored in the Window object, there are web workers which are web platforms acting as APIs, nodejs treats each script as separate modules and the scope belongs to each module.

# Chapter 5: The (Not So) Secret Lifecycle of Variables

 Hoisting allows us to access declarations in the scope even before them being declared, that is due to the execution cycle in the javascript parsing/compilation.

TDZ (Temporal Dead Zone)
As mentioned in chapter 1 javascript has a special treatment for variables declared with let and const, variables declared with this reserved keywords are part of hoisting and are initilized in the parsing phase with undefined however due to the TDZ the compiler adds an instruction in the center of the program at the point that is was declared. We won't be able to use the declaration at any point before it has been initialized.

# Chapter 7: Using Closures

Closures allow us reading variable declarations throughout the executionof nested functions even when the scope has been closed. This is possible again because of lexical environments created, each inner scope has a pointer reference to the outer scope making possible to read the variable declarations.

# Chapter 8: The Module Pattern

Modules help us to structure our code in a modular way encapsulating pieces of code and making it maintainable overtime. A module in javascript represents a set of structures, variables, functions adding them to a public API, these modules are stateless meaning the only store information to be used.
