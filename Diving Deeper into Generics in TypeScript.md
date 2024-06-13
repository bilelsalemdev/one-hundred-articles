# Diving Deeper into Generics in TypeScript

Hello everyone, السلام عليكم و رحمة الله و بركاته

Generics in TypeScript offer more than just a way to write flexible and reusable code; they provide a mechanism to create sophisticated and type-safe abstractions. By exploring advanced generic concepts, constraints, and real-world applications, you can unlock the full potential of TypeScript's type system.

## Advanced Generic Function Patterns

Let's consider some more advanced patterns with generic functions to see how they can be utilized effectively.

### Multiple Type Parameters

A function can accept multiple generic parameters, which is particularly useful when dealing with pairs or tuples of values.

```typescript
function merge<T, U>(obj1: T, obj2: U): T & U {
	return { ...obj1, ...obj2 };
}

const person = { name: "John" };
const job = { title: "Developer" };
const employee = merge(person, job);
```

In this example, `merge` combines two objects into one, preserving the types of both input objects.

### Generic Constraints with Multiple Types

By using constraints, you can ensure that generic parameters have certain properties or methods, making your functions safer and more predictable.

```typescript
interface Lengthwise {
	length: number;
}

function loggingLength<T extends Lengthwise>(arg: T): T {
	console.log(arg.length);
	return arg;
}

loggingLength("Hello"); // OK
loggingLength([1, 2, 3]); // OK
loggingLength({ length: 10, value: "Test" }); // OK
loggingLength(3); // ERROR
```

Here, the `loggingLength` function is constrained to types that have a `length` property, ensuring that the function can safely access `length` on its argument.

#### Generic Classes and Inheritance

Generics can be combined with class inheritance to create powerful, reusable components.

```typescript
class DataHolder<T> {
	private data: T;

	constructor(data: T) {
		this.data = data;
	}

	getData(): T {
		return this.data;
	}

	setData(data: T): void {
		this.data = data;
	}
}

class StringDataHolder extends DataHolder<string> {
	constructor(data: string) {
		super(data);
	}

	getUpperCaseData(): string {
		return this.getData().toUpperCase();
	}
}

const stringHolder = new StringDataHolder("hello");
console.log(stringHolder.getUpperCaseData()); // HELLO
```

In this example, `DataHolder` is a generic class, and `StringDataHolder` extends it to provide additional functionality specific to strings.

#### Generic Interfaces and Type Aliases

Generics are often used with interfaces and type aliases to create flexible data structures and function signatures.

```typescript
interface Repository<T> {
	getById(id: string): T;
	save(entity: T): void;
}

class UserRepository implements Repository<User> {
	private users: Map<string, User> = new Map();

	getById(id: string): User {
		return this.users.get(id);
	}

	save(user: User): void {
		this.users.set(user.id, user);
	}
}

type User = {
	id: string;
	name: string;
};

const repo = new UserRepository();
repo.save({ id: "1", name: "Bilel" });
console.log(repo.getById("1")); // { id: '1', name: 'Bilel' }
```

This example shows a generic `Repository` interface that can be implemented for any type, and a specific `UserRepository` that handles `User` entities.

#### Keyof and Lookup Types

TypeScript's `keyof` operator and lookup types allow for even more powerful generic constructs.

```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
	return obj[key];
}

const person = { name: "Bilel", age: 23 };
const name = getProperty(person, "name"); // Bilel
const age = getProperty(person, "age"); // 23
const wrongProp = getProperty(person, "weight"); // ERROR in typescript : it will be underlined with red line .
```

Here, `getProperty` uses `keyof` to ensure that the `key` parameter is a valid key of the `obj` parameter, and returns the correct type.

#### Conditional Types

Conditional types provide a way to express more complex type relationships.

```typescript
type MessageOf<T> = T extends { message: unknown } ? T["message"] : never;

interface Email {
	message: string;
}

interface SMS {
	message: string;
}

type EmailMessageContents = MessageOf<Email>; // string
type SMSMessageContents = MessageOf<SMS>; // string
type NumberMessageContents = MessageOf<number>; // never
```

In this example, `MessageOf` is a conditional type that extracts the `message` property type if it exists, or `never` otherwise.

#### Real-World Applications

Generics are prevalent in real-world TypeScript applications, from complex data handling to API integrations and beyond.

##### Data Transformation Utilities

Utility functions that transform data structures often benefit from generics.

```typescript
function mapArray<T, U>(arr: T[], transform: (item: T) => U): U[] {
	return arr.map(transform);
}

const numbers = [1, 2, 3];
const strings = mapArray(numbers, (num) => num.toString());
```

##### API Response Handling

When dealing with API responses, generics can ensure type safety across different endpoints.

```typescript
async function fetchJson<T>(url: string): Promise<T> {
	const response = await fetch(url);
	return response.json();
}

interface User {
	id: string;
	name: string;
}

const user = await fetchJson<User>("/api/user/1");
console.log(user.name);
```

#### Conclusion

By exploring and mastering the use of generics, including advanced patterns and constraints, you can significantly elevate the quality and efficiency of your TypeScript projects. This deeper understanding not only improves your current work but also prepares you to tackle future challenges with confidence and skill .
