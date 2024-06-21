# Understanding the Factory Method Design Pattern

Hello everyone, السلام عليكم و رحمة الله و بركاته

The Factory Method is a creational design pattern that provides an interface for creating objects in a superclass but allows subclasses to alter the type of objects that will be created. It helps in dealing with the problem of creating objects without having to specify the exact class of the object that will be created. This is particularly useful in scenarios where the creation process is complex or involves multiple steps.

### What Problem Does the Factory Method Solve?

1. **Class Instantiation Issues**: Directly instantiating objects using `new` can lead to code that is tightly coupled with specific classes. This makes the code difficult to maintain and extend.
2. **Complex Object Creation**: When object creation involves several steps or configurations, using a constructor can be cumbersome and hard to read.

3. **Subclasses Control**: It provides a way for subclasses to decide which class to instantiate, allowing for more flexible and reusable code.

### Key Concepts of the Factory Method

- **Product**: The interface or abstract class defining the objects that the factory method will create.
- **Concrete Product**: The implementation of the product interface.
- **Creator**: The abstract class or interface that declares the factory method.
- **Concrete Creator**: The class that implements the factory method to create an object of the Concrete Product class.

### Real-World Example: Document Creation

Consider a scenario where we have an application that can create different types of documents such as Word Documents, PDF Documents, and Excel Sheets. The application should be able to generate these documents without knowing their specific implementation details.

#### Step-by-Step Implementation in TypeScript

1. **Define the Product Interface**

```typescript
interface IDocument {
	open(): void;
	save(): void;
	close(): void;
}
```

2. **Concrete Products**

```typescript
class WordDocument implements IDocument {
	open(): void {
		console.log("Opening Word Document");
	}
	save(): void {
		console.log("Saving Word Document");
	}
	close(): void {
		console.log("Closing Word Document");
	}
}

class PDFDocument implements IDocument {
	open(): void {
		console.log("Opening PDF Document");
	}
	save(): void {
		console.log("Saving PDF Document");
	}
	close(): void {
		console.log("Closing PDF Document");
	}
}

class ExcelDocument implements IDocument {
	open(): void {
		console.log("Opening Excel Document");
	}
	save(): void {
		console.log("Saving Excel Document");
	}
	close(): void {
		console.log("Closing Excel Document");
	}
}
```

3. **Creator Abstract Class**

```typescript
abstract class DocumentCreator {
	public abstract createDocument(): IDocument;

	public newDocument(): void {
		const doc = this.createDocument();
		doc.open();
		doc.save();
		doc.close();
	}
}
```

4. **Concrete Creators**

```typescript
class WordDocumentCreator extends DocumentCreator {
	public createDocument(): IDocument {
		return new WordDocument();
	}
}

class PDFDocumentCreator extends DocumentCreator {
	public createDocument(): IDocument {
		return new PDFDocument();
	}
}

class ExcelDocumentCreator extends DocumentCreator {
	public createDocument(): IDocument {
		return new ExcelDocument();
	}
}
```

5. **Client Code**

```typescript
class Application {
	public static main(): void {
		let creator: DocumentCreator;

		creator = new WordDocumentCreator();
		creator.newDocument();

		creator = new PDFDocumentCreator();
		creator.newDocument();

		creator = new ExcelDocumentCreator();
		creator.newDocument();
	}
}

Application.main();
```

### Explanation

1. **IDocument Interface**: This interface defines the methods `open()`, `save()`, and `close()` that all document types must implement.
2. **Concrete Products**: `WordDocument`, `PDFDocument`, and `ExcelDocument` are classes that implement the `IDocument` interface. Each class provides its own implementation for the methods defined in the interface.

3. **DocumentCreator Abstract Class**: This abstract class declares the factory method `createDocument()`. It also provides a `newDocument()` method that calls the factory method to create a document and then performs a series of operations on it.

4. **Concrete Creators**: `WordDocumentCreator`, `PDFDocumentCreator`, and `ExcelDocumentCreator` are subclasses of `DocumentCreator`. Each subclass implements the `createDocument()` method to instantiate a specific type of document.

5. **Client Code**: The `Application` class demonstrates how to use the factory method pattern. It creates instances of different document creators and calls the `newDocument()` method to generate and operate on documents.

### Benefits of Using the Factory Method Pattern in TypeScript

- **Decoupling**: The client code (`Application`) does not need to know the exact class of the document it works with. It only interacts with the `IDocument` interface and the `DocumentCreator` abstract class.
- **Single Responsibility**: Each creator class is responsible for instantiating a specific type of document. The document classes handle their own specific behaviors.

- **Flexibility and Extensibility**: Adding a new type of document is straightforward. You can create a new class that implements the `IDocument` interface and a new creator class that extends `DocumentCreator`.

- **Maintainability**: Changes in the creation process of specific documents do not affect the client code or other document types.

By using the Factory Method pattern, you can create complex applications with different features without complicating the code, adhering to principles of clean code and design patterns.
