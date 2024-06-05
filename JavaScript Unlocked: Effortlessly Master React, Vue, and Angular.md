# JavaScript Unlocked: Effortlessly Master React, Vue, and Angular

## Introduction

If you've mastered JavaScript, you'll find that learning front-end frameworks like React, Vue, and Angular becomes significantly easier. These frameworks share many underlying principles and patterns, which means that once you become proficient in one, you'll find it much simpler to pick up the others. Understanding JavaScript fundamentals such as state management, component-based architecture, event handling, and two-way data binding will give you a strong foundation to navigate and excel in any of these frameworks.

In this guide, we'll compare React, Vue, and Angular by looking at how they handle common tasks such as state management, component lifecycle, refs, props, two-way data binding, and event emission from child to parent components. By understanding these core concepts in each framework, you'll see just how similar they are and how your JavaScript skills can be seamlessly transferred from one to another.

## State Management:

### React

In React, state management within a component is handled using hooks such as `useState`:

```javascript
import React, { useState } from "react";

function MyComponent() {
	const [count, setCount] = useState(0);

	return (
		<div>
			<p>Count: {count}</p>
			<button onClick={() => setCount(count + 1)}>Increment</button>
		</div>
	);
}
```

### Angular

In Angular, state is typically managed using services and `BehaviorSubject` from RxJS for more complex state management:

```typescript
// counter.service.ts
import { Injectable } from "@angular/core";
import { BehaviorSubject } from "rxjs";

@Injectable({
	providedIn: "root",
})
export class CounterService {
	private countSubject = new BehaviorSubject<number>(0);
	count$ = this.countSubject.asObservable();

	increment() {
		this.countSubject.next(this.countSubject.value + 1);
	}
}

// my-component.component.ts
import { Component } from "@angular/core";
import { CounterService } from "./counter.service";

@Component({
	selector: "app-my-component",
	template: `
		<div>
			<p>Count: {{ count | async }}</p>
			<button (click)="increment()">Increment</button>
		</div>
	`,
})
export class MyComponent {
	count = this.counterService.count$;

	constructor(private counterService: CounterService) {}

	increment() {
		this.counterService.increment();
	}
}
```

### Vue

In Vue, state can be managed using the `data` option for local state and Vuex for more complex state management:

```javascript
// MyComponent.vue
<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="increment">Increment</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      count: 0,
    };
  },
  methods: {
    increment() {
      this.count += 1;
    },
  },
};
</script>
```

## Component Lifecycle:

### React

React uses lifecycle methods like `useEffect` to handle side effects:

```javascript
import React, { useEffect } from "react";

function MyComponent() {
	useEffect(() => {
		console.log("Component mounted");
		return () => {
			console.log("Component unmounted");
		};
	}, []);

	return <div>My Component</div>;
}
```

### Angular

Angular lifecycle hooks are methods like `ngOnInit` and `ngOnDestroy`:

```typescript
import { Component, OnInit, OnDestroy } from "@angular/core";

@Component({
	selector: "app-my-component",
	template: "<div>My Component</div>",
})
export class MyComponent implements OnInit, OnDestroy {
	ngOnInit() {
		console.log("Component mounted");
	}

	ngOnDestroy() {
		console.log("Component unmounted");
	}
}
```

### Vue

Vue lifecycle hooks include methods like `mounted` and `beforeDestroy`:

```javascript
export default {
	template: "<div>My Component</div>",
	mounted() {
		console.log("Component mounted");
	},
	beforeDestroy() {
		console.log("Component unmounted");
	},
};
```

## Refs:

### React

React uses `useRef` to reference DOM elements:

```javascript
import React, { useRef } from "react";

function MyComponent() {
	const inputRef = useRef(null);

	const focusInput = () => {
		inputRef.current.focus();
	};

	return (
		<div>
			<input ref={inputRef} />
			<button onClick={focusInput}>Focus Input</button>
		</div>
	);
}
```

### Angular

In Angular, `ViewChild` is used to reference DOM elements:

```typescript
import { Component, ViewChild, ElementRef } from "@angular/core";

@Component({
	selector: "app-my-component",
	template: `
		<input #inputElement />
		<button (click)="focusInput()">Focus Input</button>
	`,
})
export class MyComponent {
	@ViewChild("inputElement") inputElement: ElementRef;

	focusInput() {
		this.inputElement.nativeElement.focus();
	}
}
```

### Vue

Vue uses the `ref` attribute to reference DOM elements:

```html
<template>
	<div>
		<input ref="inputElement" />
		<button @click="focusInput">Focus Input</button>
	</div>
</template>

<script>
	export default {
		methods: {
			focusInput() {
				this.$refs.inputElement.focus();
			},
		},
	};
</script>
```

## Component Props:

### React

In React, props are passed to components via attributes:

```javascript
function MyComponent({ message }) {
	return <div>{message}</div>;
}

// Usage
<MyComponent message="Hello, World!" />;
```

### Angular

In Angular, `@Input` is used to pass data to child components:

```typescript
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-my-component',
  template: '<div>{{ message }}</div>',
})
export class MyComponent {
  @Input() message: string;
}

// Usage in a parent component template
<app-my-component [message]="'Hello, World!'"></app-my-component>
```

### Vue

In Vue, props are passed via attributes in the template:

```html
<template>
	<div>{{ message }}</div>
</template>

<script>
	export default {
		props: ["message"],
	};
</script>

<!-- Usage -->
<MyComponent message="Hello, World!" />
```

## Two-Way Binding:

### React

React does not have built-in two-way binding. State is updated via callbacks:

```javascript
function MyComponent() {
	const [value, setValue] = useState("");

	return <input value={value} onChange={(e) => setValue(e.target.value)} />;
}
```

### Angular

Angular provides two-way binding with `ngModel`:

```typescript
import { Component } from "@angular/core";

@Component({
	selector: "app-my-component",
	template: '<input [(ngModel)]="value" />',
})
export class MyComponent {
	value: string = "";
}
```

### Vue

Vue offers two-way binding with the `v-model` directive:

```html
<template>
	<input v-model="value" />
</template>

<script>
	export default {
		data() {
			return {
				value: "",
			};
		},
	};
</script>
```

## Child-Parent Communication (Event Emission):

### Vue

In Vue, a child component can emit an event, and the parent component can listen for that event and respond accordingly.

**Child Component (MyChildComponent.vue):**

```html
<template>
	<button @click="notifyParent">Click me</button>
</template>

<script>
	export default {
		methods: {
			notifyParent() {
				this.$emit("childClicked", "Some data from child");
			},
		},
	};
</script>
```

**Parent Component (MyParentComponent.vue):**

```html
<template>
	<div>
		<MyChildComponent @childClicked="handleChildClick" />
	</div>
</template>

<script>
	import MyChildComponent from "./MyChildComponent.vue";

	export default {
		components: {
			MyChildComponent,
		},
		methods: {
			handleChildClick(data) {
				console.log("Event received from child:", data);
			},
		},
	};
</script>
```

### React

In React, this is typically handled by passing a callback function as a prop to the child component. The child component calls this function to notify the parent component.

**Child Component (MyChildComponent.js):**

```javascript
function MyChildComponent({ onChildClick }) {
	return (
		<button onClick={() => onChildClick("Some data from child")}>
			Click me
		</button>
	);
}

export default MyChildComponent;
```

**Parent Component (MyParentComponent.js):**

```javascript
import React from "react";
import MyChildComponent from "./MyChildComponent";

function MyParentComponent() {
	const handleChildClick = (data) => {
		console.log("Event received from child:", data);
	};

	return (
		<div>
			<MyChildComponent onChildClick={handleChildClick} />
		</div>
	);
}

export default MyParentComponent;
```

### Angular

In Angular, child components emit events using `EventEmitter`, and the parent component listens for these events.

**Child Component (my-child.component.ts):**

```typescript
import { Component, Output, EventEmitter } from "@angular/core";

@Component({
	selector: "app-my-child",
	template: '<button (click)="notifyParent()">Click me</button>',
})
export class MyChildComponent {
	@Output() childClicked = new EventEmitter<string>();

	notifyParent() {
		this.childClicked.emit("Some data from child");
	}
}
```

**Parent Component (my-parent.component.ts):**

```typescript
import { Component } from "@angular/core";

@Component({
	selector: "app-my-parent",
	template:
		'<app-my-child (childClicked)="handleChildClick($event)"></app-my-child>',
})
export class MyParentComponent {
	handleChildClick(data: string) {
		console.log("Event received from child:", data);
	}
}
```

## Summary:

- **React**: Uses hooks and props to manage state and communication. It's flexible and allows for custom solutions.
- **Vue**: Offers a balance of structure and flexibility with built-in directives like `v-model` and event emission through `$emit`.
- **Angular**: Provides a comprehensive framework with built-in solutions for state management, communication, and two-way binding using services, `EventEmitter`, and `ngModel`.

Understanding these core concepts in each framework will allow you to leverage your JavaScript skills and easily transition between React, Vue, and Angular, making you a versatile and effective front-end developer.
