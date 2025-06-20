# A Unified, Type-Safe Database Adapter System for Multi-DB Applications

## Database Adapters System

A flexible, database-agnostic interface that allows your application to work with multiple database types through a unified API.

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Supported Databases](#supported-databases)
- [Quick Start](#quick-start)
- [Usage Examples](#usage-examples)
- [Creating Custom Adapters](#creating-custom-adapters)
- [Migration Guide](#migration-guide)
- [API Reference](#api-reference)
- [Best Practices](#best-practices)

## Overview

The Database Adapters System provides a generic interface that abstracts database-specific operations, allowing you to:

- **Switch between databases** without changing business logic
- **Support multiple databases** in the same application
- **Maintain type safety** across all database operations
- **Preserve backward compatibility** with existing MongoDB code
- **Extend easily** to support new database types

## Architecture

```
┌─────────────────────┐
│   Business Logic    │
├─────────────────────┤
│ IDatabaseService    │  ← Generic Interface
├─────────────────────┤
│ DatabaseService     │  ← Implementation
├─────────────────────┤
│   Database Adapters │
├─────────────────────┤
│ MongoDB │ PostgreSQL│  ← Database-specific
│  MySQL  │  SQLite   │     adapters
└─────────────────────┘
```

### Core Components

1. **Generic Types** (`src/common/types/common.types.ts`)

   - `DatabaseOperator` - Standardized query operators
   - `DatabaseCondition` - Generic condition structure
   - `DatabaseQuery` - Complete query specification
   - `DatabaseAdapter` - Adapter interface contract

2. **Database Service** (`src/common/database/services/database.service.ts`)

   - Unified API for all database operations
   - Adapter management and switching
   - Backward compatibility layer

3. **Adapters** (`src/common/database/adapters/`)

   - `MongoDBAdapter` - MongoDB translation
   - `PostgreSQLAdapter` - PostgreSQL SQL generation
   - Extensible for additional databases

4. **Factory** (`src/common/database/factories/database-adapter.factory.ts`)
   - Centralized adapter creation and management
   - Runtime adapter switching support

## Supported Databases

| Database   | Status     | Adapter Class       | Notes                          |
| ---------- | ---------- | ------------------- | ------------------------------ |
| MongoDB    | ✅ Full    | `MongoDBAdapter`    | Default, backward compatible   |
| PostgreSQL | ✅ Full    | `PostgreSQLAdapter` | SQL generation with parameters |
| MySQL      | 🚧 Planned | `MySQLAdapter`      | Coming soon                    |
| SQLite     | 🚧 Planned | `SQLiteAdapter`     | Coming soon                    |
| Redis      | 🚧 Planned | `RedisAdapter`      | For caching/sessions           |

## Quick Start

### 1. Basic Usage (MongoDB - Default)

```typescript
import { DatabaseService } from "src/common/database/services/database.service";

@Injectable()
export class UserService {
  constructor(private readonly databaseService: DatabaseService) {}

  async findActiveUsers() {
    // Create a simple filter - works with any database
    const filter = this.databaseService.filterEqual("status", "active");
    return filter; // MongoDB: { status: { $eq: 'active' } }
  }
}
```

### 2. Switching to PostgreSQL

```typescript
import { DatabaseService } from "src/common/database/services/database.service";
import { DatabaseAdapterFactory } from "src/common/database/factories/database-adapter.factory";

@Injectable()
export class UserService {
  constructor(
    private readonly databaseService: DatabaseService,
    private readonly adapterFactory: DatabaseAdapterFactory
  ) {
    // Switch to PostgreSQL
    const pgAdapter = this.adapterFactory.createAdapter("postgresql");
    this.databaseService.setAdapter(pgAdapter);
  }

  async findActiveUsers() {
    const filter = this.databaseService.filterEqual("status", "active");
    return filter; // PostgreSQL: { whereClause: 'WHERE status = $1', parameters: ['active'] }
  }
}
```

## Usage Examples

### Building Complex Queries

```typescript
import { DatabaseOperator } from "src/common/types/common.types";

// Create individual conditions
const conditions = [
  this.databaseService.createCondition(
    "age",
    DatabaseOperator.GREATER_THAN,
    18
  ),
  this.databaseService.createCondition(
    "email",
    DatabaseOperator.CONTAINS,
    "@company.com"
  ),
  this.databaseService.createCondition("status", DatabaseOperator.IN, [
    "active",
    "premium",
  ]),
];

// Build complex query
const query = this.databaseService.buildQuery(conditions, "AND", {
  limit: 10,
  offset: 0,
  sort: [{ field: "createdAt", direction: "DESC" }],
});

// Execute with current adapter
const result = this.databaseService.executeQuery(query);
```

### Common Filter Operations

```typescript
// Text search
const nameFilter = this.databaseService.filterContain("name", "John", {
  caseSensitive: false,
  fullWord: false,
});

// Date range
const dateFilter = this.databaseService.filterDateBetween(
  "createdAt",
  "updatedAt",
  new Date("2023-01-01"),
  new Date("2023-12-31")
);

// Array operations
const categoryFilter = this.databaseService.filterIn("category", [
  "tech",
  "business",
]);
const excludeFilter = this.databaseService.filterNin("status", [
  "banned",
  "deleted",
]);

// Existence checks
const profileFilter = this.databaseService.filterExists("profile", true);

// Numeric comparisons
const scoreFilter = this.databaseService.filterGreaterThan("score", 100);
```

### Runtime Adapter Switching

```typescript
@Injectable()
export class DatabaseManager {
  constructor(
    private readonly databaseService: DatabaseService,
    private readonly adapterFactory: DatabaseAdapterFactory
  ) {}

  async switchDatabase(dbType: string) {
    if (this.adapterFactory.isSupported(dbType)) {
      const adapter = this.adapterFactory.createAdapter(dbType);
      this.databaseService.setAdapter(adapter);
      console.log(`Switched to ${dbType} adapter`);
    } else {
      throw new Error(`Unsupported database type: ${dbType}`);
    }
  }

  getSupportedDatabases(): string[] {
    return this.adapterFactory.getSupportedTypes();
  }
}
```

## Creating Custom Adapters

### 1. Implement the DatabaseAdapter Interface

```typescript
import { Injectable } from "@nestjs/common";
import {
  DatabaseAdapter,
  DatabaseQuery,
  DatabaseCondition,
  DatabaseSort,
  DatabaseOperator,
} from "src/common/types/common.types";

@Injectable()
export class MySQLAdapter implements DatabaseAdapter {
  readonly name = "mysql";

  translateQuery(query: DatabaseQuery): { sql: string; parameters: any[] } {
    // Implement MySQL-specific query translation
    const conditions = query.conditions.map((condition) =>
      this.translateCondition(condition)
    );

    const whereClause =
      conditions.length > 0
        ? `WHERE ${conditions
            .map((c) => c.condition)
            .join(` ${query.logic || "AND"} `)}`
        : "";

    const parameters = conditions.flatMap((c) => c.parameters);

    return {
      sql: whereClause,
      parameters,
    };
  }

  translateCondition(condition: DatabaseCondition): {
    condition: string;
    parameters: any[];
  } {
    const { field, operator, value } = condition;

    switch (operator) {
      case DatabaseOperator.EQUAL:
        return { condition: `${field} = ?`, parameters: [value] };

      case DatabaseOperator.GREATER_THAN:
        return { condition: `${field} > ?`, parameters: [value] };

      case DatabaseOperator.CONTAINS:
        return {
          condition: `${field} LIKE ?`,
          parameters: [`%${value}%`],
        };

      // Add more operators as needed
      default:
        throw new Error(`Unsupported operator: ${operator}`);
    }
  }

  translateSort(sort: DatabaseSort[]): string {
    if (sort.length === 0) return "";

    const orderClauses = sort.map((s) => `${s.field} ${s.direction}`);
    return `ORDER BY ${orderClauses.join(", ")}`;
  }
}
```

### 2. Register the Adapter

```typescript
// In your module or service
import { MySQLAdapter } from "./adapters/mysql.adapter";

@Injectable()
export class DatabaseSetupService {
  constructor(private readonly adapterFactory: DatabaseAdapterFactory) {
    // Register custom adapter
    this.adapterFactory.registerAdapter("mysql", () => new MySQLAdapter());
  }
}
```

### 3. Use the Custom Adapter

```typescript
const mysqlAdapter = this.adapterFactory.createAdapter("mysql");
this.databaseService.setAdapter(mysqlAdapter);

// Now all operations use MySQL adapter
const filter = this.databaseService.filterEqual("id", 123);
// Output: { sql: 'WHERE id = ?', parameters: [123] }
```

## Migration Guide

### From MongoDB-Specific to Generic

#### Before (MongoDB-specific)

```typescript
// Old way - MongoDB only
const mongoFilter = {
  status: { $eq: "active" },
  age: { $gt: 18 },
  email: { $regex: /.*@company\.com/i },
};
```

#### After (Database-agnostic)

```typescript
// New way - works with any database
const conditions = [
  this.databaseService.createCondition(
    "status",
    DatabaseOperator.EQUAL,
    "active"
  ),
  this.databaseService.createCondition(
    "age",
    DatabaseOperator.GREATER_THAN,
    18
  ),
  this.databaseService.createCondition(
    "email",
    DatabaseOperator.CONTAINS,
    "@company.com"
  ),
];

const query = this.databaseService.buildQuery(conditions, "AND");
const filter = this.databaseService.executeQuery(query);
```

### Backward Compatibility

All existing MongoDB code continues to work:

```typescript
// These still work unchanged
const legacyFilter = this.databaseService.filterEqual("status", "active");
const legacySearch = this.databaseService.filterContain("name", "John");
const legacyDate = this.databaseService.filterDateBetween(
  "start",
  "end",
  date1,
  date2
);
```

## API Reference

### DatabaseOperator Enum

```typescript
enum DatabaseOperator {
  EQUAL = "eq",
  NOT_EQUAL = "ne",
  GREATER_THAN = "gt",
  GREATER_THAN_OR_EQUAL = "gte",
  LESS_THAN = "lt",
  LESS_THAN_OR_EQUAL = "lte",
  IN = "in",
  NOT_IN = "nin",
  CONTAINS = "contains",
  STARTS_WITH = "startsWith",
  ENDS_WITH = "endsWith",
  REGEX = "regex",
  EXISTS = "exists",
  IS_NULL = "isNull",
  BETWEEN = "between",
}
```

### IDatabaseService Interface

#### Generic Query Methods

- `createCondition<T>(field, operator, value, options?)` - Create a condition
- `buildQuery(conditions, logic?, options?)` - Build complex query
- `executeQuery(query)` - Execute query with current adapter

#### Convenience Methods

- `filterEqual<T>(field, value)` - Equal filter
- `filterNotEqual<T>(field, value)` - Not equal filter
- `filterContain(field, value, options?)` - Text contains filter
- `filterIn<T>(field, values)` - IN filter
- `filterNin<T>(field, values)` - NOT IN filter
- `filterDateBetween(start, end, startDate, endDate)` - Date range filter
- `filterGreaterThan<T>(field, value)` - Greater than filter
- `filterLessThan<T>(field, value)` - Less than filter
- `filterExists(field, exists)` - Existence filter

#### Adapter Management

- `setAdapter(adapter)` - Set database adapter
- `getAdapter()` - Get current adapter
- `getSupportedDatabases()` - List supported databases

### DatabaseAdapterFactory

- `createAdapter(type)` - Create adapter by type
- `getSupportedTypes()` - List all supported types
- `isSupported(type)` - Check if type is supported
- `registerAdapter(type, factory)` - Register custom adapter

## Best Practices

### 1. Adapter Selection Strategy

```typescript
// Environment-based adapter selection
const dbType = process.env.DATABASE_TYPE || "mongodb";
const adapter = this.adapterFactory.createAdapter(dbType);
this.databaseService.setAdapter(adapter);
```

### 2. Error Handling

```typescript
try {
  const adapter = this.adapterFactory.createAdapter(dbType);
  this.databaseService.setAdapter(adapter);
} catch (error) {
  console.error(`Failed to initialize ${dbType} adapter:`, error.message);
  // Fallback to default
  const defaultAdapter = this.adapterFactory.createAdapter("mongodb");
  this.databaseService.setAdapter(defaultAdapter);
}
```

### 3. Type Safety

```typescript
// Use specific types for better type safety
interface UserFilter {
  status: "active" | "inactive";
  age: number;
  email: string;
}

const userCondition = this.databaseService.createCondition<
  UserFilter["status"]
>("status", DatabaseOperator.EQUAL, "active");
```

### 4. Performance Considerations

```typescript
// Cache adapters for frequently used databases
const adapterCache = new Map<string, DatabaseAdapter>();

function getCachedAdapter(type: string): DatabaseAdapter {
  if (!adapterCache.has(type)) {
    adapterCache.set(type, this.adapterFactory.createAdapter(type));
  }
  return adapterCache.get(type)!;
}
```

### 5. Testing Different Adapters

```typescript
describe("Database Operations", () => {
  const testCases = ["mongodb", "postgresql"];

  testCases.forEach((dbType) => {
    describe(`with ${dbType}`, () => {
      beforeEach(() => {
        const adapter = adapterFactory.createAdapter(dbType);
        databaseService.setAdapter(adapter);
      });

      it("should filter active users", () => {
        const filter = databaseService.filterEqual("status", "active");
        expect(filter).toBeDefined();
        // Add adapter-specific assertions
      });
    });
  });
});
```

## Open Source & Contributing

This Database Adapter System is being released as an open-source project on GitHub to encourage collaboration and community-driven improvement.

🔗 **GitHub Repository:** \[_Coming Soon / https://github.com/bilelsalemdev/universal-db-adapter/tree/main_]

Everyone is welcome to contribute — whether it's:

- Writing unit and integration tests
- Completing or improving existing adapters (e.g., MySQL, SQLite, Redis)
- Suggesting enhancements or new features
- Reporting issues or edge cases
- Improving documentation and usage examples

If you're interested in helping shape a flexible, database-agnostic abstraction layer for modern applications, feel free to fork the repo, submit PRs, or start a discussion.

Let’s build it together! 💪
