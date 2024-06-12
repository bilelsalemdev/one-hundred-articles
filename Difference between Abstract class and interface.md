# TypeScript Tales: Unraveling Abstracts and Interfaces

Hello everyone, السلام عليكم و رحمة الله وبركاته!

In the vast world of Object-Oriented Programming (OOP), two fundamental concepts often discussed are abstract classes and interfaces. Understanding their differences and knowing when to use each can greatly enhance your code's structure and maintainability. Let's delve into the nuances of abstract classes and interfaces in TypeScript, exploring their similarities, differences, and the scenarios where one might be preferred over the other.

## Introduction:

Imagine we're architecting a software system, and at its core, we have a class representing a building:

```typescript
class Building {}
```

But what if we want to create a blueprint for a generic building, something that other specific types of buildings can inherit from or implement? This is where the concepts of abstract classes and interfaces come into play.

### Abstract Classes:

Consider this modification to our `Building` class:

```typescript
abstract class Building {}
```

By adding the `abstract` keyword, we've transformed `Building` into an abstract class. An abstract class serves as a blueprint for other classes and cannot be instantiated on its own. It can contain both implemented and abstract (unimplemented) methods.

### Interfaces:

Now, let's introduce interfaces. Unlike abstract classes, interfaces are purely structural contracts. They define the shape of an object but do not provide any implementation details. Here's an example:

```typescript
interface Constructable {
	construct(): void;
}
```

This `Constructable` interface specifies that any implementing class must have a `construct` method without providing its implementation.

## Similarities:

### Blueprint for Objects:

Both abstract classes and interfaces provide blueprints for other classes to follow. They establish contracts that concrete classes must adhere to, ensuring consistency and predictability in the codebase.

### Inheritance and Implementation:

Abstract classes support inheritance, allowing subclasses to extend their functionality. Similarly, interfaces support implementation, enabling classes to fulfill their contract by implementing the interface's methods.

## Differences:

### Implementation:

Abstract classes can contain both implemented and abstract methods, providing a partial implementation to subclasses. In contrast, interfaces only define method signatures, leaving the implementation details to the implementing classes.

### Single vs. Multiple Inheritance:

While a TypeScript class can extend only one abstract class, it can implement multiple interfaces. This flexibility allows classes to conform to multiple contracts, promoting code reuse and modularity.

## Choosing Between Them:

### Abstract Classes:

Use abstract classes when you have a base implementation that can be shared among multiple subclasses. They are ideal for situations where you need to enforce a common structure while providing some default behavior.

#### Real-world Example:

Consider a scenario where you're modeling different types of vehicles. You might have an abstract class `Vehicle` with common properties like `speed` and `fuelType`, along with abstract methods like `start` and `stop`.

```typescript
abstract class Vehicle {
	speed: number;
	fuelType: string;

	constructor(speed: number, fuelType: string) {
		this.speed = speed;
		this.fuelType = fuelType;
	}

	abstract start(): void;
	abstract stop(): void;
}

class Car extends Vehicle {
	start() {
		console.log("Car starting...");
	}

	stop() {
		console.log("Car stopping...");
	}
}

class Bike extends Vehicle {
	start() {
		console.log("Bike starting...");
	}

	stop() {
		console.log("Bike stopping...");
	}
}
```

### Interfaces:

Use interfaces when you want to define a contract that multiple unrelated classes can adhere to. They are great for promoting loose coupling and enabling polymorphic behavior.

#### Real-world Example:

Imagine you're building a system with various components that need to be serializable. You can create an interface `Serializable` with a method `serialize`, allowing any class implementing it to be serialized.

```typescript
interface Serializable {
	serialize(): string;
}

class User implements Serializable {
	name: string;
	age: number;

	constructor(name: string, age: number) {
		this.name = name;
		this.age = age;
	}

	serialize() {
		return JSON.stringify({
			name: this.name,
			age: this.age,
		});
	}
}

class Product implements Serializable {
	id: number;
	price: number;

	constructor(id: number, price: number) {
		this.id = id;
		this.price = price;
	}

	serialize() {
		return JSON.stringify({
			id: this.id,
			price: this.price,
		});
	}
}
```

## Conclusion:

In conclusion, abstract classes and interfaces are tools for defining contracts and promoting code reuse. Understanding their differences and knowing when to use each will make you a more effective developer. Choose wisely, and may your coding journey be filled with growth and success! Happy coding!
