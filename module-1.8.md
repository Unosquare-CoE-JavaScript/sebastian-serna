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
