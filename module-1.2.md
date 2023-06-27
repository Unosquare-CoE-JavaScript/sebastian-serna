# Scope and Closures

# Chapter 1: What's the scope?

Javascript se conoce como un lenguaje de scripts por lo que se asume que la ejecución de sus programas son procesados en una sola lectura pero en realidad el lenguaje tiene una fase de lectura de parsing/compilation en el que al realizar la declaración de variables y funciones el lenguaje se encarga de mover todas estas declaraciones al inicio del script jugando un papel importante en el scope, este proceso es conocido como hoisting.

# Chapter 2: Illustrating Lexical Scope

Hemos escuchado que las variables definidas con var tienen un scope global y las definidas con let y const un scope de bloque, en el momento de ejecución javascript crea ambientes lexicos/burbujas en los cuales viven las declaraciones de estas variables, al momento del codigo ejecutarse en una lectura javascript preguntara en que ambiente lexico se encuentra y buscara la declaración de las variables en el ambiente lexico y usara la asignación mas cercana para así determinar que valor sera leido en la ejecución.

# Chapter 3: The Scope Chain

Como ya se mencionó en los capitulos previos los ambientes lexicos se crean por cada bloque asi formando una cadena de scopes y determina el camino en que las variables seran accesibles, esta cadena realiza la lectura de adentro hacía afuera. Esto tambien aplica para las arrow functions

# Chapter 4: Around the Global Scope

Dependiendo de los ambientes en que se este ejecutando javascript el motor se encarga de asignar este scope global, en web y navegadores las declaraciones del script se almacenan en el objeto Window, existen los web workers que son plataformas en un navegador y actuan como APIS, Node trata cada archivo como su propio module y el scope pertenece a cada modulo

# Chapter 5: The (Not So) Secret Lifecycle of Variables

Hoisting nos permite acceder a declaraciones de funciones en el scope incluso antes de que estas sean definidas, debido al ciclo de ejecución de javascript en la fase de parsing/compilation nos permite hacer esto

TDZ (Temporal Dead Zone)
Javascript tiene un comportamiento especial con declaraciones de variables con let y const, las variables declaradas con estas palabras reservadas hacen parte del hoisting y son inicializadas en la fase de parsing con undefined mas sin embargo debido al TDZ, dado que el compilador agrega una instrucción en el centro del programa en el punto en el que se declaró. No podemos usar la variable en ningún momento antes de que ocurra esa inicialización

# Chapter 7: Using Closures

Los Closures nos permiten la lectura de valores definidos de una variable atravez de la ejecución de funciones anidadas incluso cuando ese scope ya ha sido cerrado. Esto es posible nuevamente por el ambiente lexico interno y externo que se crean al definir las funciones, cada ambiente lexico interno tiene una referencia que apunta al externo haciendo posible la lectura de las variables en estos ambientes

# Chapter 8: The Module Pattern

Los modulos nos ayudan a estructurar nuestro codigo de manera modular encapsulando partes de codigo y haciendolo mantenible en el tiempo.

Un modulo en javascript representa una colección de estructuras, variables, funciones, etc agregandolos a una API publica, estos modulos no tienen estado solo mantienen información para ser reutilizada.
