# Design patterns

Design patterns are common architectural approaches that give us typical solutions to common problems in software design. Design patterns are usually categorized into 3 use cases, creational patterns, structural patterns and behavioral patterns

### SOLID Design Principles

SOLID is a set of principles to write more scalable and maintainable code, it is used throughout many patterns.

**S (Single Responsibility Principle):** This is a very simple principle because it just states that a class, method, function should do its responsibility only

```js
class Journal {
	static count = 0;

	constructor() {
		this.entries = {};
	}
	
	addEntry(text) {
		let c = ++Jornal.count;
		let entry = `${c}: ${text}`;
		this.entries[c] = entry;
		return c;
	}

	/**
	save(filename) {
		fs.writeFileSync(filename);
	}

	loadFromUrl() {...}
	**/
	
}
```

The methods ```save``` and ```loadFromUrl``` violate the single responsibility principle, the ```Journal``` class should only be meant to represent a real life journal but these methods write and load files to the system thus creating more responsibility to the ```Journal``` class

What would happen if the ```Journal``` model isn't the only object in your application that needs to be written or loaded to the system? We would have to write that logic to each object, writing and loading responsibility should be delegated to another class

```js
import fs from 'fs';

class PersistenceManager {
	save(filename) {
		fs.writeFileSync(filename);
	}

	loadFromUrl() {...}
}
```

**O (Open-Closed Principle):** This principle states that a class or a part of our system should be open for extension but closed por modification

```js
let COLOR = Object.freeze({
	red: 'red',
	green: 'green',
	blue: 'blue',
});

class Product {
	constructor(name, color) {
		this.name = name;
		this.color = color;
	}
}

class ProductFilter {
	filterByColor(products, color) {
		return products.filter(p => p.color === color);
	}
}

const apple = new Product('Apple', COLOR.red);
const tree = new Product('Tree', COLOR.green);

const products = [apple, tree];
const pf = new ProductFilter();
pf.filterByColor(products, COLOR.red);
```

The class ```ProductFilter``` violates the principle when we want to not only filter by color but filter by size too, we would have to modificate the class. We can rely on inheritance to follow the Open-Closed principle

```js
class ColorOption {
	constructor(color) {
		this.color = color;
	}
	
	matches(item) {
		return item.color === this.color;
	}
}

// new filter to extend
class SizeOption {
	constructor(size) {
		this.size = size;
	}
	
	matches(item) {
		return item.size === this.size;
	}
}

class ProductFilter {
	// the second argument can be a ColorOption, SizeOption or other options we may need and create
	filter(items, filterOption) {
		return items.filter(x => filterOption.matches(x));
	}
}

const apple = new Product('Apple', COLOR.red);
const tree = new Product('Tree', COLOR.green);

const products = [apple, tree];
const pf = new ProductFilter();

pf.filter(products, new ColorOption(COLOR.red));
```

**L (Liskov Substitution Principle):** This principles states that a if a class B is a subtype of class A then objects of class A may be replaced by objects of class B without altering the behavior of our program

```js
class Rectangle { 
	constructor(height, width) { 
		this.height = height; 
		this.width = width;
	} 
	setHeight(newHeight) { 
		this.height = newHeight; 
	}
} 
class Square extends Rectangle {} 

const rectangle = new Rectangle(4, 5);
const square = new Square(4, 4);
console.log(`Height: ${rectangle.height}, Width: ${rectangle.width}`); // Outputs 'Height: 4, Width: 5' (correct)
console.log(`Height: ${square.height}, Width: ${square.width}`); // Outputs 'Height: 4, Width: 4' (correct) 
square.setHeight(5);
console.log(`Height: ${square.height}, Width: ${square.width}`); // Outputs 'Height: 5, Width: 4' (wrong)
```

In this example we initialize a  `Rectangle`  and  `Square`, and output their dimensions. We then call the  `Rectangle.setHeight()`  on the Square object, and output its dimensions again. What we find is that the square now has a different height than its length, which of course makes for an invalid square.

This can be solved, using polymorphism, an if statement in the Rectangle class, or a variety of other methods. But the real cause of the issue is that  `Square`  is not a good child class of  `Rectangle`, and that in reality, perhaps both shapes should inherit from a  `Shape`  class instead

**I (Interface Segregation Principle):** This principle states that no code should be forced to depend on methods it does not use.

```js
class Machine {
	constructor() {
		if (this.constructot.name === 'Machine') {
			throw new Error('Machine is abstract!');
		}
	}
	
	print(doc) {...}
	fax(doc) {...}
	scan(doc) {...}
}

class Printer extends Machine {
	print(doc) {...}
	fax(doc) {
		throw new Error('I am a printer and I can not fax');
	}
	scan(doc) {...}
}
```

When the class `Printer` implements the method `fax` from the superclass it throws an error because printers can't fax than in itself is violating the principle because it is dependant on a method that it does not use

```js
class FaxMethod {
	constructor() {...}
	fax() {...}
}

class PrintMethod {
	constructor() {...}
	print() {...}
}

class Printer extends PrinterMethod {
	print() {...}
}

class Fax extends FaxMethod {
	fax() {...}
}
```

We can't extend multiple classes in javascript so a work around to this principle would be to build a class that recieves this methods as parameters since we can't implement interfaces in this case.

**D (Dependency Inversion Principle):** The dependency inversion principle states that high level code should never depend on low level interfaces, and should instead use abstractions. It’s all about decoupling code.

```js
class PurchaseHandler {
    processPayment(paymentDetails, amount) {
        // Complicated, PayPal specific logic goes here
        const paymentSuccess = PayPal.requestPayment(paymentDetails, amount);
        if (paymentSuccess) {
            // Do something
            return true;
        }
        // Do something
        return false;
    }
}
```

The problem here is that if we change from PayPal to Square (another payment processor) in 6 months time, this code breaks. We need to go back and swap out our PayPal API calls for Square API calls. But in addition, what if the Square API wants different types of data? Or perhaps it wants us to “stage” a payment first, and then to process it once staging has completed?

That’s bad, and so we need to abstract the functionality out instead.

Rather than directly call the PayPal API from our payment page, we’ll instead create another class called  `PaymentHandler`. The interface for this class will remain the same no matter what underlying payment system we use, even if the two systems are completely different. We’ll still need to make changes to the  `PaymentHandler`  interface if we change payment processor, but our higher level interface remains unchanged.

```js
class PurchaseHandler {
    processPayment(paymentDetails, amount) {
        const paymentSuccess = PaymentHandler.requestPayment(
            paymentDetails,
            amount
        );

        if (paymentSuccess) {
            // Do something
            return true;
        }

        // Do something
        return false;
    }
}

class PaymentHandler {
    requestPayment(paymentDetails, amount) {
        // Complicated, PayPal specific logic goes here
        return PayPal.requestPayment(paymentDetails, amount);
    }
}
```

# Creational Design Patterns

**Builder:** This pattern makes you construct complex objects step by step. The pattern allows you to produce different types and represetations of an object using the same construction code.

```js
class Tag {...}

class HtmlBuilder {
	constructor(rootName) {
		this.root = new Tag(rootName);
		this.rootName = rootName;
	}

	addChild(childName, childText) {
		const child = new Tag(childName, childText);
		this.root.children.push(child);
	}
	
	build() {
		return this.root;
	}
}

// The builder creates the tag in the constructor and provides us with a addChild method to create the child tags
const builder = new HtmlBuilder('ul');
builder.addChild('li', word);
```

**Factory: ** This pattern provides an interface for creating objects in a superclass, but allows subclasses to alter the type of objects that will be created.

```js
class Point {...}
class PolarPoint extends Point {...}
class CardinalPoint extends Point {...}

class PointFactory {
	// It's going to return a Point
	factoryMethod() {...}
}

class PolarPointFactory extends PointFactory {
	factoryMethod() {
		return new PolarPoint();
	}
}

class CardinalPointFactory extends PointFactory {
	factoryMethod() {
		return new CardinalPoint();
	}
}

function pointUsage(pointFactory) {
	pointCreator.factoryMethod();
}

// you can pass any instance of a creator
pointUsage(new PolarPointFactory()); // Returns a PolarPoint
pointUsage(new CardinalPointFactory()); // Returns a CardinalPoint
```

**Abstract Factory: ** Is a pattern that lets you produce families of related objects without specifying their concrete classes.

```js
class HotDrink {
	consume() {}
}

class Tea extends HotDrink {
	consume() {...}
}

class Coffe extends HotDrink {
	consume() {...}
}

class HotDrinkFactory {
	prepare(amount) {...}
}

class TeaFactory extends HotDrinkFactory {
	prepare(amount) {
		return new Tea();
	}
}

class CoffeFactory extends HotDrinkFactory {
	prepare(amount) {
		return new Coffe();
	}
}

const AvailableDrink = Object.freeze({
	coffe: CoffeFactory,
	tea: TeaFactory,
});

class HotDrinkMachine {

	constructor() {
		this.factories = {};
		for(let drink in AvailableDrink) {
			this.factories[drink] = new AvailableDrink[drink]();
		}
	}
	
	interact(drinkFactory) {
		return drinkFactory.prepare();
	}
}

const machine = new HotDrinkMachine();
machine.drinkFactory(new CoffeFactory()); // returns a Coffe
```

**Prototype:** This pattern lets you copy existing objects without making your code dependent on their classes.

```js
/**
 * The example class that has cloning ability. We'll see how the values of field
 * with different types will be cloned.
 */
class Prototype {
    primitive;
    component;
    circularReference;

    clone() {
        const clone = Object.create(this);

        clone.component = Object.create(this.component);

        // Cloning an object that has a nested object with backreference
        // requires special treatment. After the cloning is completed, the
        // nested object should point to the cloned object, instead of the
        // original object. Spread operator can be handy for this case.
        clone.circularReference = {
            ...this.circularReference,
            prototype: { ...this },
        };

        return clone;
    }
}

class ComponentWithBackReference {
    prototype;

    constructor(prototype) {
        this.prototype = prototype;
    }
}

/**
 * The client code.
 */
function clientCode() {
    const p1 = new Prototype();
    p1.primitive = 245;
    p1.component = new Date();
    p1.circularReference = new ComponentWithBackReference(p1);

    const p2 = p1.clone();
    if (p1.primitive === p2.primitive) {
        console.log('Primitive field values have been carried over to a clone. Yay!');
    } else {
        console.log('Primitive field values have not been copied. Booo!');
    }
    if (p1.component === p2.component) {
        console.log('Simple component has not been cloned. Booo!');
    } else {
        console.log('Simple component has been cloned. Yay!');
    }

    if (p1.circularReference === p2.circularReference) {
        console.log('Component with back reference has not been cloned. Booo!');
    } else {
        console.log('Component with back reference has been cloned. Yay!');
    }

    if (p1.circularReference.prototype === p2.circularReference.prototype) {
        console.log('Component with back reference is linked to original object. Booo!');
    } else {
        console.log('Component with back reference is linked to the clone. Yay!');
    }
}

clientCode();
```

**Singleton:** Is a creational design pattern that lets you ensure that a class has only one instance, while providing a global access point to this instance.

```js
class Singleton {
	constructor() {
		const instance = this.constructor.instance;
		// check if an instance has already been created
		if (instance) {
			return instance;
		}
		this.constructor.instance = this;
	}
}
```

# Structural Design Patterns

**Adapter:** allows objects with incompatible interfaces to collaborate

```js
class Point {...}
class Line {...}
class VectorObject extends Array {...}
class VectorRectangle extends VectorObject {...}

// This function is only made to work with points
// We need an adapter to work with vectors
const drawPoint = function(point) {
	process.stdout.write('.');
}

class LineToPointAdapter extends Array {
	constructor(line) {
		super();
		// code to turn the Line into a Point
	}
}

const vectorObjects = [
	new VectorRectangle(),
	new VectorRectangle(),
];

let drawPoints = function() {
	for (let vo of vectorObjects) {
		for (let line of vo) {
			const adapter = new LineToPointAdapter(line);
			adapter.forEach(drawPoint);
		}
	}
}
```

**Bridge:** This pattern lets you split a large class or a set of closely related classes into two separate hierarchies—abstraction and implementation—which can be developed independently of each other.

```js
// Shape Hierarchy
class Shape {
	constructor(renderer) {
		this.renderer = renderer;
	}
	
	draw() {
		this.renderer.renderCircle(this.radius);
	}
}
class Circle {...}
class Square {...}

// Render Hierarchy
class Renderer {
	renderCircle(radius) {...}
}
class Raster {...}
class Vector {...}

const vector = new VectorRenderer();
// when passing vector to the circle we're creating a bridge to couple the two classes but they're independant
const circle = new Circle(vector, 5);
```

**Composite:** This pattern lets you compose objects into tree structures and then work with these structures as if they were individual objects.

```js
class GraphicObject {
	constructor(name) {
		this._name = name;
		// the children property is going to hold the tree of structures
		this.children = [];
	}
}

class Circle extends GraphicObject {
	constructor() {
		super('Circle');
	}
}

const drawing = new GraphicObject();
// children is going to store any object we may define
drawing.children.push(new Circle());
```
**Decorator:** This pattern lets you attach new behaviors to objects by placing these objects inside special wrapper objects that contain the behaviors.

```js
class Shape {
	constructor(color) {
		// how do we add the behavior to the Circle to print the color
		this.color = color;
	}
}
class Circle extends Shape {
	constructor(radius=0) {
		super();
		this.radius = radius;
	}
	resize(factor) {...}

	toString() {
		return `A circle of radius ${this.radius}`;
	}
}

// Decorator to wrap the classes
class ColoredShaped extends Shape {
	constructor(shape, color) {
		super();
		this.shape = shape;
		this.color = color;
	}

	// override
	toString() {
		// reuse the parent class implementation and add additional behavior 
		return `${this.shape.toString()} has the color ${this.color}`;
	}
}

const circle = new Circle(2);
// create the wrapper and pass in the shape
const redCircle = new ColoredShape(circle, 'red');
```

**Facade:** This is a pattern that provides a simplified interface to a library, a framework, or any other complex set of classes.

```js
class Buffer extends Array {
	constructor(width, height) {
		super();
		this.width = width;
		this.height = height;
		this.alloc(width*height);
	}

	write(text, position) {...}
}

class Viewport {
	constructor(buffer = new Buffer()) {
		this.buffer = buffer;
		this.offset = 0;
	}

	append(text, post) {
		this.buffer.write(text, pos + this.offset);
	}
}

// Facade for the previous classes
class Console {
	constructor() {
		this.buffer = new Buffer();
		this.currentViewport = new Viewport(this.buffer);
		this.buffers = [this.buffer];
		this.viewports = this[this.currentViewport];
	}

	write(text) {
		this.currentViewport.buffer.write(text);
	}
}

const c = new Console();
c.write();
```

**Flyweight:** This pattern lets you fit more objects into the available amount of RAM by sharing common parts of state between multiple objects instead of keeping all of the data in each object.

```js
class FormattedText {
	constructor(plainText) {
		this.plainText = plainText;
		this.caps = new Array(plainText.length).map(() => false);
	}

	capitalize(start, end) {
		for (let i = start; i <= end; i++) {
			this.caps[i] = true;	
		}
	}
	
	toString() {...}
}

class FlyweightFormattedText {
	constructor(plainText) {
		this.plainText = plainText;
		this.formatting = [];
		// to capitalize the text we need to share the state to another object
	}

	getRange(start, end) {
		const range = new TextRange(start, end);
		this.formatting.push(range);
		return range;
	}
}

// will hold the state for capitalization
class Range {...}

const text = 'My text';
const ft = new FormattedText(text);
ft.capitalize(10, 15);

const fft = new FlyweightFormattedText(text);
fft.getRange(16, 19).capitalize = true;
```

**Proxy: ** This pattern lets you provide a substitute or placeholder for another object. A proxy controls access to the original object, allowing you to perform something either before or after the request gets through to the original object.

```js
class Subject {
    request() {}
}

class RealSubject extends Subject {
    public request() {
        console.log('RealSubject: Handling request.');
    }
}

class Proxy extends Subject {
    /**
     * The Proxy maintains a reference to an object of the RealSubject class. It
     * can be either lazy-loaded or passed to the Proxy by the client.
     */
    constructor(realSubject) {
        this.realSubject = realSubject;
    }

    /**
     * The most common applications of the Proxy pattern are lazy loading,
     * caching, controlling the access, logging, etc. A Proxy can perform one of
     * these things and then, depending on the result, pass the execution to the
     * same method in a linked RealSubject object.
     */
    request() {
        if (this.checkAccess()) {
            this.realSubject.request();
            this.logAccess();
        }
    }

    checkAccess() {
        // Some real checks should go here.
        console.log('Proxy: Checking access prior to firing a real request.');

        return true;
    }

    logAccess() {
        console.log('Proxy: Logging the time of request.');
    }
}

/**
 * The client code is supposed to work with all objects (both subjects and
 * proxies) via the Subject interface in order to support both real subjects and
 * proxies. In real life, however, clients mostly work with their real subjects
 * directly. In this case, to implement the pattern more easily, you can extend
 * your proxy from the real subject's class.
 */
function clientCode(subject: Subject) {
    // ...

    subject.request();

    // ...
}

console.log('Client: Executing the client code with a real subject:');
const realSubject = new RealSubject();
clientCode(realSubject);

console.log('');

console.log('Client: Executing the same client code with a proxy:');
const proxy = new Proxy(realSubject);
clientCode(proxy);
```

# Behavioral Design Patterns

**Chain of responsibility:** This pattern lets you pass requests along a chain of handlers. Upon receiving a request, each handler decides either to process the request or to pass it to the next handler in the chain.

```js
class Handler {
	setNext() {...}
	handle() {...}
}

class MainHandler extends Handler {
	nextHandler;
	setNext(handler) {...}
	handle(request) {...}
}

class BossHandler extends MainHandler {
	handle(request) {
		...
		return super.handle(request);
	}
}

class EmployeeHandler extends MainHandler {
	handle(request) {
		...
		return super.handle(request);
	}
}

class ClientHandler extends MainHandler {...}

const boss = new BossHandler();
const employee = new EmployeeHandler();

// Chain the responsibilities
boss.setNext(employee).setNext(client);
```

**Command: ** This pattern turns a request into a stand-alone object that contains all information about the request.

```js
class BankAccount {
	constructor(balance=0) {
		this.balance = balance;
	}

	deposit(amount) {...}
	withdraw(amount) {...}
}

class BankAccountCommand {
	constructor(account, action, amount) {
		this.account = account;
		this.action = action;
		this.amount = amount;
	}

	call() {
		switch(this.action) {
			case 'deposit':
				this.account.deposit(this.amount);
				break;
		}
	}
}

const bankAccount = new BankAccount();
const command = new BankAccountCommand(bankAccount, 'deposit',50);
command.call();
```

**Iterator:** This pattern lets you traverse elements of a collection without exposing its underlying representation (list, stack, tree, etc.).

```js
class Stuff {
	constructor() {
		this.a = 11;
		this.b = 22;
	}
	[Symbol.iterator]() {
		let i = 0;
		let self = this;
		return {
			next: function() {
				done: i > 1,
				value: self[i++ === 0 ? 'a' : 'b']
			}
		}	
	}
}

const values = [100, 200, 300];
const stuff = new Stuff();
for (let item of stuff.backwards) {
	console.log(`${item}`);
}
```

**Mediator:** This pattern lets you reduce chaotic dependencies between objects. The pattern restricts direct communications between the objects and forces them to collaborate only via a mediator object.

```js
class Person {
	constructor(name) {
		this.name = name;
		this.chatLog = [];
	}

	recieve(sender, message) {...}
	say(message) {...}
}

// This mediator class lets us interact with instances of the Person class
class ChatRoom {
	constructor() {
		// people stores records of the Person class
		this.people = [];
	}

	join() {...}
	broadcast(source, message) {...}
	message(source, destination, message) {...}
}

const chatRoom = new ChatRoom();
const john = new Person('John');
const jane = new Person('Jane');

room.join(john);
room.join(jane);

john.say('Hi');
```

**Memento:** This pattern lets you save and restore the previous state of an object without revealing the details of its implementation.

```js
class BankAccount {
	constructor(balance) {...}
	deposit(amount) {
		...
		return new Memento(this.balance);
	}
	restore(memento) {
		this.balance = m.balance;
	}
}

// Holds the state for the bank account balance
class Memento {
	constructor(balance) {...}
}

const bankAccount = new BankAccount(100);
const m1 = ba.deposit(50);
const m2 = ba.deposit(25);
ba.restore(m1);
```

**Observer:** This pattern allows some objects to notify other objects about changes in their state.

```js
class Event {
	constructor() {
		this.handlers = new Map();
		this.count = 0;
	}

	subscribe(handler) {
		this.handlers.set(++this.count, handler);
		return this.count;
	}
	unsubscribe(idx) {
		this.handlers.delete(idx);
	}

	fire(sender, args) {
		this.handlers.forEach((v,k) => v(sender, args));
	}
}

class Person {...}

const person = new Person();
const sub = person.fallsIll.subscribe((s,a) => {
	// s is the subscription value
	// a is an additional arguments
});
```

**State:** This pattern allows an object to change the behavior when its internal state changes.

```js
const State = Object.freeze({
	offHook: 'off hook',
	...
});

const Trigger = Object.freeze({
	callDialed: 'dialNumber',
	...
});

const rules = {};
rules[State.offHook] = [
	{
		trigger: Trigger.callDialed,
		state: State.connecting
	}
];

const state = State.offHook;
const exitState = State.onHook;

// here we change the behavior when the state changes
const getInput = function() {
	const prompt = ['My state'];
	for(let i = 0; i < rules[state].length; i++) {
		const t = rules[state][i].trigger;
		prompt.push(`${i}. ${t}`);
	}
}
```

**Strategy:** This pattern turns a set of behaviors into objects and makes them interchangeable inside original context object.

```js

const OutputFormat = Object.freeze({...});
class TextProcessor {
	constructor(outputFormat) {
		this.buffer = [];
		this.setOutputFormat(outputFormat);
	}

	setOutputFormat(format) {...}
	appendListItem(items) {
		// in this method we append the new objects to the original object interchangeably
		this.listStrategy.start(this.buffer);
		for (let item of items) {
			this.listStrategy.addListItem(this.buffer, item);
		}
		this.listStrategy.end(this.buffer);
	}
}

class ListStrategy {
	start(buffer) {}
	end(bugger) {}
	addListItem(buffer, item) {}
}

class HtmlListStrategy extends ListStrategy {
	start(buffer) {
		buffer.push('<ul>');
	}
	end(buffer) {
		buffer.push('</ul>');
	}
	addListItem(buffer, item) {
		buffer.push(`<li>${item}</li>`);
	}
}
```

**Template Method:** This pattern allows you to define a skeleton of an algorithm in a base class and let subclasses override the steps without changing the overall algorithm’s structure.

```js
class Game {
	...
	// template method
	run() {
		this.start();
		while(!this.haveWinner) {
			this.takeTurn();
		}
	}
}

class Chess extends Game {
	constructor() {
		super();
		this.maxTurns = 10;
		this.turn = 1;
	}
	
	// override the methods from the parent class but not the template method
	
}

const chess = new Chess();
chess.run();
```

**Visitor: ** This pattern allows adding new behaviors to existing class hierarchy without altering any existing code.

```js
class NumberExpression {
	constructor() {
		this.value = value;
	}
	
	print(buffer) {
		buffer.push(this.value.toString());
	}
}

class AdditionExpression {
	constructor(left, right) {
		this.left = left;
		this.right = right;
	}
	
	print(buffer) {
		buffer.push('(');
		this.left.print(buffer);
		buffer.push('+');
		this.right.print(buffer);
		buffer.push(')');
	}
}

const expression = new AdditionExpression(
	new NumberExpression(1),
	new AdditionExpression(
		new NumberExpression(2),
		new NumberExpression(3)
	)
);

const buffer = [];
// The buffer is the visitor it traverses each expression and adds additional behavior without altering the classes
expression.print(buffer);
```
