# Intro to TypeScript

Hello everyone, السلام عليكم و رحمة الله و بركاته

## Introduction

Typing is essential for any language to ensure a friendly user experience. TypeScript is a statically typed language built on top of JavaScript, enhancing its syntax and providing powerful tools for extracting needed data from any request. This article will discuss:

- Types in TypeScript
- Interfaces
- Enums
- Operators like union and intersection
- Generics and how to make components reusable using generics

## Types in TypeScript

In TypeScript, types can be categorized into primitive types and complex types:

### Primitive Types

Primitive types include:

- **number**: Includes integers, floats, and decimals
- **bigint**: Represents whole numbers larger than 2^53 - 1
- **string**: Represents text data
- **boolean**: Represents true or false values

### Complex Types

Complex types include:

- **arrays**: Collections of elements
- **objects**: Collections of key-value pairs
- **enums**: Named constants
- **tuples**: Arrays with a fixed number of elements of specified types
- **never**: Represents values that never occur
- **void**: Represents the absence of a value, commonly used as the return type of functions that do not return a value
- **null**: Represents a null value
- **undefined**: Represents an undefined value
- **any**: Represents any type, opting out of type checking

## Interfaces

Interfaces in TypeScript define the structure of an object. They are used to describe the shape that an object should have, making it easier to ensure that objects conform to certain criteria.

### Example:

```typescript
interface User {
	id: number;
	name: string;
	email?: string; // optional property
}

let user: User = {
	id: 1,
	name: "Bilel",
};
```

### Interfaces vs. Types

Both `interface` and `type` can be used to define the shape of an object, but there are some differences:

- `interface` can be extended, making it easy to add new properties or methods.
- `type` can represent a wider range of types (not just object shapes), including unions and intersections.

### Example of extending an interface:

```typescript
interface Animal {
	name: string;
}

interface Bird extends Animal {
	fly: string;
}
```

## Enums

Enums allow you to define a set of named constants, making it easier to manage sets of related values.

### Example:

```typescript
enum Direction {
	Up,
	Down,
	Left,
	Right,
}

let move: Direction = Direction.Up;
```

### Enums vs. Union Types

- **Enums**: Provide a clear, self-documenting way to define sets of related constants.
- **Union Types**: Can be used to achieve similar functionality with more flexibility.

### Example of Union Type:

```typescript
type Direction = "Up" | "Down" | "Left" | "Right";

let move: Direction = "Up";
```

## Operators: Union and Intersection

### Union Types

Union types allow a variable to be one of several types.

### Example:

```typescript
let value: number | string;
value = 22; // valid
value = "Hello"; // also valid
```

### Intersection Types

Intersection types combine multiple types into one, ensuring that the resulting type has all the properties of the combined types.

### Example:

```typescript
interface Name {
	firstName: string;
	lastName: string;
}

interface Contact {
	email: string;
	phone: string;
}

type Person = Name & Contact;

let person: Person = {
	firstName: "Bilel",
	lastName: "Salem",
	email: "bilelsalemdev@gmail.com",
	phone: "22222222",
};
```

## Generics

Generics enable you to create reusable components that can work with any type. They provide a way to write functions, classes, or interfaces that can operate with different data types while retaining type safety.

### Example:

```typescript
function identity<T>(arg: T): T {
	return arg;
}

let output1 = identity<string>("Hello");
let output2 = identity<number>(22);
```

### Reusable Components with Generics

Generics are particularly useful for creating reusable and flexible components.

### Example:

```typescript
interface Box<T> {
	content: T;
}

let stringBox: Box<string> = { content: "Hello" };
let numberBox: Box<number> = { content: 22 };
```

## Conclusion

TypeScript enhances JavaScript by adding static types, making the code more flexible and powerful. By understanding and utilizing types, interfaces, enums, operators like union and intersection, and generics, you can write more predictable and reusable code.
