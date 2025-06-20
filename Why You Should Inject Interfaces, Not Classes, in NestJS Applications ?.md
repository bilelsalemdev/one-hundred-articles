# Why You Should Inject Interfaces, Not Classes, in NestJS Applications ?

Injecting **interfaces** instead of concrete **classes** in NestJS constructors (or any DI-based architecture) promotes **decoupling**, **flexibility**, and **testability**. However, because interfaces don't exist at runtime in TypeScript/JavaScript, this approach has practical limitations and requires additional setup (like `useClass`, `useExisting`, or `useFactory`).

---

### ✅ Benefits of Injecting Interfaces Instead of Classes

#### 1. **Loose Coupling**

- **Problem**: Injecting concrete classes tightly couples your service to a specific implementation.
- **Benefit**: Injecting an interface allows you to depend on a **contract**, not a specific implementation.
- **Example**:

  ```ts
  constructor(private readonly userService: IUserService) {}
  ```

#### 2. **Improved Testability**

- You can easily **mock** implementations in tests by providing a test-specific class or object.
- Example:

  ```ts
  providers: [
    {
      provide: IUserService,
      useClass: MockUserService,
    },
  ];
  ```

#### 3. **Implementation Swapping**

- Swap one implementation for another without changing the consuming code.
- Useful for different environments (e.g., mock vs. real, in-memory vs. database, local vs. cloud).

#### 4. **Domain-Driven Design (DDD) Alignment**

- Encourages defining clear contracts (`interfaces`) between domain services, infrastructure, and application layers.
- Supports clean architecture and separation of concerns.

#### 5. **Avoid Circular Dependencies**

- When using interfaces and `useClass` or `useExisting`, you can often structure the code to avoid circular imports.

---

### ⚠️ Caveat in TypeScript and NestJS

- TypeScript interfaces **do not exist at runtime**, so you **cannot inject an interface directly** using the interface name alone.
- You must use **tokens**, often created with a `Symbol` or `InjectionToken`.

---

### 🛠 How to Inject an Interface in NestJS

```ts
// user-service.interface.ts
export interface IUserService {
  getUserById(id: string): Promise<User>;
}

// create a token
export const IUserServiceToken = Symbol('IUserService');

// in module
{
  provide: IUserServiceToken,
  useClass: UserService, // or a mock in test
}

// in constructor
constructor(@Inject(IUserServiceToken) private readonly userService: IUserService) {}
```

---

### 🧠 Summary

| Benefit                        | Explanation                            |
| ------------------------------ | -------------------------------------- |
| 🔗 Loose Coupling              | Decouples consumer from implementation |
| 🧪 Testability                 | Easier mocking and unit testing        |
| 🔄 Flexibility                 | Swap implementations easily            |
| 📐 Clean Architecture          | Enforces contracts, aligns with DDD    |
| 🔁 Avoid Circular Dependencies | Reduces tight runtime coupling         |
