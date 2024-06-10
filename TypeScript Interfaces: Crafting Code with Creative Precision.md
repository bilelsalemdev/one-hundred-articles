# TypeScript Interfaces: Crafting Code with Creative Precision

In TypeScript, interfaces play a crucial role in defining the shape of objects and providing a contract for their behavior. They allow us to enforce consistency and ensure that our code adheres to a specific structure. Let's explore deeply what interfaces are.

# Table of Contents

- [TypeScript Interfaces: Crafting Code with Creative Precision](#typescript-interfaces-crafting-code-with-creative-precision)
- [Table of Contents](#table-of-contents)
  - [Introduction to Interfaces](#introduction-to-interfaces)
  - [Creating Interfaces](#creating-interfaces)
  - [Optional Properties](#optional-properties)
  - [Readonly Properties](#readonly-properties)
  - [Extending Interfaces](#extending-interfaces)
  - [Implementing Interfaces](#implementing-interfaces)
  - [Private Properties](#private-properties)
- [Conclusion](#conclusion)

## Introduction to Interfaces

I will take a real world example to explain what interfaces are. Let's say you are building a car. You have a blueprint that defines the structure of the car, such as the number of wheels, the color, the model, etc. This blueprint is like an interface in TypeScript. It defines the shape of an object and enforces certain properties that the object must have.

## Creating Interfaces

To create an interface in TypeScript, you use the `interface` keyword followed by the name of the interface and the properties it should have. An example of an interface that defines the structure of a `User` object would be great to understand this concept:

```typescript
interface User {
	id: number;
	name: string;
	email: string;
	age: number;
}

let user: User = {
	id: 1,
	name: "Bilel salem",
	email: "bilelsalemdev@gmail.com",
	age: 23,
};
```

## Optional Properties

if your `user` object has this code :

```typescript
let user: User = {
	id: 1,
	name: "Bilel salem",
	email: "bilelsalemdev@gmail.com",
};
```

It will throw an error because the `age` property is missing. So what if I have in my database some users that don't have an age? This is where optional properties come in. You can make a property optional by adding a `?` after the property name `age` in the interface definition:

```typescript
interface User {
	id: number;
	name: string;
	email: string;
	age?: number;
}
```

Now, the `age` property is optional, and you can create a `user` object without it:

```typescript
let user: User = {
	id: 1,
	name: "Bilel salem",
	email: "bilelsalemdev@gmail.com",
};
```

This will not throw an error because the `age` property is optional.

## Readonly Properties

Sometimes, you may want to create an object with properties that cannot be changed after they are set. As an example, let's say you have a `Car` interface with a `model` property that should not be changed once it is set. If you try to change the `model` property after it is set like this:

```typescript
interface Car {
	model: string;
}

let car: Car = { model: "BMW" };
car.model = "Audi";
```

this will be correct in TypeScript, but what if you want to prevent this from happening? You can make a property readonly by adding the `readonly` keyword before the property name in the interface definition:

```typescript
interface Car {
	readonly model: string;
}

let car: Car = { model: "BMW" };
car.model = "Audi"; // Error: Cannot assign to 'model' because it is a read-only property.
```

Now, if you try to change the `model` property after it is set, TypeScript will throw an error.

## Extending Interfaces

Interfaces can be extended to create new interfaces that inherit the properties of existing interfaces. This is useful when you want to define a new interface that has all the properties of an existing interface, plus some additional properties. Here's an example of extending an interface:

```typescript
interface User {
	id: number;
	name: string;
	email: string;
}

interface UserWithAge extends User {
	age: number;
}

let user: User = {
	id: 1,
	name: "Bilel salem",
	email: "bilelsalemdev@gmail.com",
};

let userWithAge: UserWithAge = {
	id: 1,
	name: "Bilel salem",
	email: "bilelsalemdev@gamail.com",
	age: 23,
};
```

## Implementing Interfaces

Now that you know how to create interfaces, you can use them to enforce a contract on classes. When a class implements an interface, it must provide an implementation for all the properties and methods defined in the interface. Here's an example of implementing an interface `User` in a class `UserImpl`:

```typescript
interface User {
	id: number;
	name: string;
	email: string;
}

class UserImpl implements User {
	id: number;
	name: string;
	email: string;

	constructor(id: number, name: string, email: string) {
		this.id = id;
		this.name = name;
		this.email = email;
	}
}

class WrongUserImpl implements User {
	id: number;
	name: string;
	email: string;

	constructor(id: number, name: string) {
		this.id = id;
		this.name = name;
	}
}
// Error: Class 'WrongUserImpl' incorrectly implements interface 'User'. Property 'email' is missing in type 'WrongUserImpl' but required in type 'User'.
```

In this example, the `UserImpl` class implements the `User` interface by providing an implementation for the `id`, `name`, and `email` properties. If you try to create a class that implements the `User` interface but does not provide an implementation for all the required properties, TypeScript will throw an error.

## Private Properties

Now that you know how to enforce the classes to behave as you want, you can also enforce the access level of the properties. If you want to remove the access to age property from the user object, you can use the `private` keyword before the property name in the interface definition:

```typescript
interface User {
	id: number;
	name: string;
	email: string;
	age: number;
}

class UserImpl implements User {
	id: number;
	name: string;
	email: string;
	private _age: number;

	constructor(id: number, name: string, email: string, age: number) {
		this.id = id;
		this.name = name;
		this.email = email;
		this._age = age;
	}
}

let user: User = new UserImpl(1, "Bilel salem", "bilelsalemdev#gmail.com", 23);
console.log(user.age);

// Error: Property 'age' is private and only accessible within class 'UserImpl'.
```

So what if you want to access the age property? You can create a getter method in the class to access the private property:

```typescript
interface User {
	id: number;
	name: string;
	email: string;
	age: number;
}

class UserImpl implements User {
	id: number;
	name: string;
	email: string;
	private _age: number;

	constructor(id: number, name: string, email: string, age: number) {
		this.id = id;
		this.name = name;
		this.email = email;
		this._age = age;
	}

	get age(): number {
		return this._age;
	}
}

let user: User = new UserImpl(1, "Bilel salem", "bilelsalemdev#gmail.com", 23);
console.log(user.age); // 23
```

# Conclusion

Interfaces are a powerful feature of TypeScript that allow you to define the shape of objects and enforce a contract on their behavior. They provide a way to ensure consistency and type safety in your code, making it easier to catch errors.
