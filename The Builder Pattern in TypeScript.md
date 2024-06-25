# The Builder Pattern in TypeScript

ASSALAMUALAIKUM WARAHMATULLAHI WABARAKATUH, السلام عليكم و رحمة اللّه و بركاته

## Introduction

The Builder Pattern is a creational design pattern that allows for the step-by-step construction of complex objects. Unlike other patterns, it provides a clear separation between the construction and representation of an object. This makes it particularly useful when an object requires multiple initialization parameters or complex configurations.

### Key Components of the Builder Pattern

1. **Builder Interface**: Defines the methods required for building the different parts of the product.
2. **Concrete Builder**: Implements the Builder interface and provides specific implementations for each part of the product.
3. **Product**: The complex object being built.
4. **Director**: (Optional) Orchestrates the construction process by using the builder interface.

## Advantages of Using the Builder Pattern

- **Improved Readability**: Construction logic is encapsulated within the builder, making the code easier to understand.
- **Flexibility**: Different configurations of an object can be created without altering the core structure.
- **Immutability**: Final products are often immutable, reducing the risk of errors.

## Implementing the Builder Pattern in TypeScript

Let's dive deep into a practical example to understand how the Builder Pattern can be implemented in TypeScript.

### Example Scenario: Building a User Profile

Consider a scenario where we need to create a `UserProfile` object with several optional and mandatory fields. The Builder Pattern is an excellent choice for handling this complexity.

### Step-by-Step Implementation

#### Step 1: Define the UserProfile

```typescript
class UserProfile {
	public firstName: string;
	public lastName: string;
	public age?: number;
	public email?: string;
	public address?: string;

	constructor(builder: UserProfileBuilder) {
		this.firstName = builder.firstName;
		this.lastName = builder.lastName;
		this.age = builder.age;
		this.email = builder.email;
		this.address = builder.address;
	}
}
```

#### Step 2: Create the Builder Class

```typescript
class UserProfileBuilder {
	public firstName: string;
	public lastName: string;
	public age?: number;
	public email?: string;
	public address?: string;

	constructor(firstName: string, lastName: string) {
		this.firstName = firstName;
		this.lastName = lastName;
	}

	setAge(age: number): UserProfileBuilder {
		this.age = age;
		return this;
	}

	setEmail(email: string): UserProfileBuilder {
		this.email = email;
		return this;
	}

	setAddress(address: string): UserProfileBuilder {
		this.address = address;
		return this;
	}

	build(): UserProfile {
		return new UserProfile(this);
	}
}
```

#### Step 3: Use the Builder to Create Objects

```typescript
const userProfile = new UserProfileBuilder("Bilel", "Salem")
	.setAge(23)
	.setEmail("bilelsalemdev@gmail.com")
	.setAddress("Tunisia")
	.build();

console.log(userProfile);
```

### Explanation

- **Product Class (`UserProfile`)**: Represents the object being built.
- **Builder Class (`UserProfileBuilder`)**: Contains the fields and methods for setting optional parameters.
- **Method Chaining**: Each method in the builder returns `this`, allowing for chaining.

## More Complex Example: Building a Configurable Computer System

We'll create a Computer object that can be configured with various components such as CPU, GPU, RAM, storage, cooling system, power supply, and additional peripherals. Each of these components may have optional configurations, making the construction process intricate.

### Step 1: Define the Product

We'll define the `Computer` class, representing the complex object to be built.

```typescript
class Computer {
	public cpu: string;
	public gpu?: string;
	public ram: number;
	public storage: { type: string; capacity: number }[];
	public coolingSystem?: string;
	public powerSupply: string;
	public peripherals?: string[];

	constructor(builder: ComputerBuilder) {
		this.cpu = builder.cpu;
		this.gpu = builder.gpu;
		this.ram = builder.ram;
		this.storage = builder.storage;
		this.coolingSystem = builder.coolingSystem;
		this.powerSupply = builder.powerSupply;
		this.peripherals = builder.peripherals;
	}
}
```

### Step 2: Create the Builder Class with Validation and Default Configurations

Next, we'll create the `ComputerBuilder` class, which will handle the construction of the `Computer` object, including validation and default configurations.

```typescript
class ComputerBuilder {
	public cpu: string;
	public gpu?: string;
	public ram: number;
	public storage: { type: string; capacity: number }[];
	public coolingSystem?: string;
	public powerSupply: string;
	public peripherals?: string[];

	constructor(cpu: string, ram: number, powerSupply: string) {
		this.cpu = cpu;
		this.ram = ram;
		this.powerSupply = powerSupply;
		this.storage = [];
	}

	addGPU(gpu: string): ComputerBuilder {
		this.gpu = gpu;
		return this;
	}

	addStorage(type: string, capacity: number): ComputerBuilder {
		this.storage.push({ type, capacity });
		return this;
	}

	addCoolingSystem(coolingSystem: string): ComputerBuilder {
		this.coolingSystem = coolingSystem;
		return this;
	}

	addPeripherals(peripherals: string[]): ComputerBuilder {
		this.peripherals = peripherals;
		return this;
	}

	validate(): void {
		if (!this.cpu) {
			throw new Error("CPU is required.");
		}
		if (this.ram <= 0) {
			throw new Error("RAM must be greater than 0.");
		}
		if (!this.powerSupply) {
			throw new Error("Power supply is required.");
		}
	}

	build(): Computer {
		this.validate();
		return new Computer(this);
	}

	static defaultGamingPC(): ComputerBuilder {
		return new ComputerBuilder("AMD Ryzen 9", 32, "850W Power Supply")
			.addGPU("NVIDIA RTX 4090")
			.addStorage("SSD", 2048)
			.addCoolingSystem("Advanced Liquid Cooling")
			.addPeripherals(["Gaming Keyboard", "Gaming Mouse", "VR Headset"]);
	}

	static defaultOfficePC(): ComputerBuilder {
		return new ComputerBuilder("Intel Core i7", 16, "600W Power Supply")
			.addStorage("SSD", 512)
			.addPeripherals(["Ergonomic Keyboard", "Wireless Mouse"]);
	}
}
```

### Step 3: Use the Builder to Create Objects

Now, let's use the `ComputerBuilder` to create various configurations of a `Computer` object, including custom configurations and default configurations.

```typescript
// Custom Gaming PC Configuration
const gamingPC = new ComputerBuilder("Intel Core i9", 32, "750W Power Supply")
	.addGPU("NVIDIA RTX 3080")
	.addStorage("SSD", 1024)
	.addStorage("HDD", 2048)
	.addCoolingSystem("Liquid Cooling")
	.addPeripherals(["Mechanical Keyboard", "Gaming Mouse", "RGB Lighting"])
	.build();

// Custom Office PC Configuration
const officePC = new ComputerBuilder("Intel Core i5", 16, "500W Power Supply")
	.addStorage("SSD", 512)
	.addPeripherals(["Standard Keyboard", "Optical Mouse"])
	.build();

// Default Gaming PC Configuration
const defaultGamingPC = ComputerBuilder.defaultGamingPC().build();

// Default Office PC Configuration
const defaultOfficePC = ComputerBuilder.defaultOfficePC().build();

console.log("Custom Gaming PC:", gamingPC);
console.log("Custom Office PC:", officePC);
console.log("Default Gaming PC:", defaultGamingPC);
console.log("Default Office PC:", defaultOfficePC);
```

### Some Explations

1. **Computer Class**: Represents the configurable computer system with various components.
2. **ComputerBuilder Class**: Manages the construction process, including validation and default configurations.
3. **Method Chaining**: Ensures that the builder methods can be called in a readable and fluid manner.
4. **Validation**: Ensures that essential components are present and valid before building the object.
5. **Default Configurations**: Provides easy access to pre-defined configurations for common use cases.

## Conclusion

The Builder Pattern is a tool for constructing complex objects with multiple optional and mandatory fields. It separates the construction process from the representation.
