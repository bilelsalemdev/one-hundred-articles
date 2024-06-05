# Introduction to Dependency Inversion and Dependency Injection with NestJS

Hello everyone, السلام عليكم و رحمة الله و بركاته

Today, I will delve into the concepts of Dependency Inversion and Dependency Injection, using NestJS as the example framework. These principles are crucial for writing maintainable, testable, and scalable code in modern software development. Let's explore these concepts through an example implementation involving file storage strategies.

### Dependency Inversion Principle (DIP)

The Dependency Inversion Principle (DIP) is one of the five SOLID principles of object-oriented design. It states that:

1. High-level modules should not depend on low-level modules. Both should depend on abstractions (e.g., interfaces).
2. Abstractions should not depend on details. Details should depend on abstractions.

This principle helps in decoupling the software modules, making the system more modular and easier to maintain.

### Dependency Injection (DI)

Dependency Injection is a design pattern that implements DIP. It allows a class to receive its dependencies from an external source rather than creating them itself. This can be done via constructor injection, method injection, or property injection. NestJS supports DI out-of-the-box using decorators.

### Implementing Dependency Injection in NestJS

To illustrate these concepts, let's create a file storage system where we can switch between different storage strategies (e.g., Dropbox, Amazon S3) without changing the business logic.

#### Step 1: Define an Interface for the Storage Strategy

First, we define an interface `IStorageStrategy` that outlines the methods any storage strategy should implement:

```typescript
export interface IStorageStrategy {
	uploadFile(file: Express.Multer.File, req?: Request): Promise<any>;
	_setAccessToken(): Promise<void>;
	download(file: string): Promise<Readable>;
	deleteFile(path: string): Promise<boolean>;
	getPresignedUrl(filepath: string): Promise<string>;
}
```

#### Step 2: Implement the Storage Strategies

Next, we implement the interface for different storage providers. Let's start with Dropbox:

```typescript
import { Injectable } from "@nestjs/common";

@Injectable()
export class DropboxStorageStrategy implements IStorageStrategy {
	async _setAccessToken(): Promise<void> {
		// Implement logic to set access token
	}

	async uploadFile(file: Express.Multer.File, req?: Request): Promise<any> {
		// Implement logic to upload file to Dropbox
	}

	async download(file: string): Promise<Readable> {
		// Implement logic to download file from Dropbox
	}

	async deleteFile(path: string): Promise<boolean> {
		// Implement logic to delete file from Dropbox
	}

	async getPresignedUrl(filepath: string): Promise<string> {
		// Implement logic to get presigned URL from Dropbox
	}
}
```

Similarly, you would implement `S3StorageStrategy` for Amazon S3:

```typescript
import { Injectable } from "@nestjs/common";

@Injectable()
export class S3StorageStrategy implements IStorageStrategy {
	async _setAccessToken(): Promise<void> {
		// Implement logic to set access token
	}

	async uploadFile(file: Express.Multer.File, req?: Request): Promise<any> {
		// Implement logic to upload file to S3
	}

	async download(file: string): Promise<Readable> {
		// Implement logic to download file from S3
	}

	async deleteFile(path: string): Promise<boolean> {
		// Implement logic to delete file from S3
	}

	async getPresignedUrl(filepath: string): Promise<string> {
		// Implement logic to get presigned URL from S3
	}
}
```

#### Step 3: Create a Service that Uses the Storage Strategy

We create a service `MulterConfigService` that depends on `IStorageStrategy`. Using DI, we inject the appropriate strategy at runtime:

```typescript
import { Injectable, Inject } from "@nestjs/common";
import { MulterModuleOptions } from "@nestjs/platform-express";
import { IStorageStrategy } from "./interfaces/storage-strategy.interface";

@Injectable()
export class MulterConfigService {
	constructor(
		@Inject("STORAGE_STRATEGY")
		private readonly storageStrategy: IStorageStrategy,
	) {}

	createMulterOptions(): MulterModuleOptions {
		return {
			storage: this.storageStrategy, // Adjust this line based on actual implementation
		};
	}
}
```

#### Step 4: Define Providers

Next, we define the providers to inject the correct storage strategy. In this example, we'll use Dropbox by default:

```typescript
export const StorageStrategyProvider = [
	{
		provide: "STORAGE_STRATEGY",
		useClass: DropboxStorageStrategy, // or S3StorageStrategy
	},
];
```

#### Step 5: Configure the Module

Finally, we configure the `AppModule` to include our providers and service:

```typescript
import { Module } from "@nestjs/common";
import { AppController } from "./app.controller";
import { MulterConfigService } from "./multer-config.service";
import { StorageStrategyProvider } from "./storage-strategy.provider";
import { DropboxStorageStrategy } from "./strategies/dropbox-storage.strategy";
import { S3StorageStrategy } from "./strategies/s3-storage.strategy";

@Module({
	imports: [],
	controllers: [AppController],
	providers: [
		...StorageStrategyProvider,
		DropboxStorageStrategy,
		S3StorageStrategy,
		MulterConfigService,
	],
})
export class AppModule {}
```

### Conclusion

By adhering to the Dependency Inversion Principle and using Dependency Injection, we have created a flexible and maintainable system where the storage strategy can be easily swapped out without modifying the core business logic. This makes our application more modular and easier to extend in the future.

By following these principles and patterns, you can build robust applications in NestJS that are well-architected and maintainable.
