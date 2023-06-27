# Understanding Asynchronous code

El código síncrono se ejecuta línea por línea, esto significa que el motor debe esperar a que una expresión finalice para continuar a la siguiente, por ende generando un bloqueo. El código asíncrono nos permite evitar esta casuística.

# Event Loop

Javascript es un lenguaje de un solo hilo y la manera en que este nos permite escribir código asíncrono es mediante el event loop. El event loop es una API que nos proveen los ambientes en los que se ejecuta javascript, podemos describir al event loop como una estructura de datos que almacena pilas de llamados a funciones implementando el patrón first in/ first out formando así una fila de los procesos a ser ejecutados.

# Callbacks

Normalmente conocemos a los callbacks como funciones que se pasan como argumento de una función A a una función B para que esta sea llamada despues de que otro bloque de código haya sido ejecutado

# Promesas

Las promesas son un objecto de javascript que representa la finalización o falla de una operación, cuando el estado de esta operación concluya la promesa nos retornará un valor. Podemos capturar este valor con los metodos que nos provee la promesa.

# Async/Await

Estas son palabras reservadas de javascript que nos ayudan a simplificar las promesas, nos permite capturar los valores retornados de una promesa en forma síncrona

# Generators

Los generators son objectos producidos por una función generadora, estos valores son entregados en demanda con el metodo .next() la cual nos ayuda a gestionar el código asíncrono de una forma controlada

Estas son las caracteristicas del lenguaje que nos permiten hacer uso de la asíncronia en javascript
