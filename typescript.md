# TypeScript Interview Questions

## Basic Questions

### 1. What is TypeScript?
TypeScript is a strongly-typed superset of JavaScript that compiles to plain JavaScript. It adds optional static typing, classes, interfaces, and other features to JavaScript, helping developers catch errors during development.

### 2. What are the benefits of using TypeScript?
- Static type checking
- Better IDE support with IntelliSense
- Early error detection
- Improved code maintainability
- Enhanced refactoring capabilities
- Better documentation through type annotations
- Support for latest JavaScript features

### 3. What are the basic types in TypeScript?
- `number` - for numeric values
- `string` - for text
- `boolean` - for true/false
- `array` - for arrays
- `tuple` - for fixed-length arrays with specific types
- `enum` - for named constants
- `any` - for dynamic types
- `void` - for functions with no return value
- `null` and `undefined`
- `never` - for functions that never return
- `object` - for non-primitive types

### 4. What is the difference between `interface` and `type` in TypeScript?
- **Interface**: Can be extended and merged, primarily used for object shapes
- **Type**: Can represent primitive types, unions, tuples, and more complex types
- Interfaces can be reopened to add new properties (declaration merging)
- Types cannot be reopened once created

### 5. What are Generics in TypeScript?
Generics provide a way to create reusable components that work with multiple types while maintaining type safety.

```typescript
function identity<T>(arg: T): T {
    return arg;
}

let output = identity<string>("Hello");
```

## Intermediate Questions

### 6. Explain the concept of Type Guards
Type guards are expressions that perform runtime checks to narrow down types within a conditional block.

```typescript
function isString(value: unknown): value is string {
    return typeof value === 'string';
}

function example(value: string | number) {
    if (isString(value)) {
        // TypeScript knows value is string here
        console.log(value.toUpperCase());
    }
}
```

### 7. What are Union and Intersection types?
- **Union types** (`|`): A value can be one of several types
- **Intersection types** (`&`): Combines multiple types into one

```typescript
type StringOrNumber = string | number;
type Person = { name: string } & { age: number };
```

### 8. What is the `readonly` modifier?
The `readonly` modifier prevents properties from being changed after initialization.

```typescript
interface User {
    readonly id: number;
    name: string;
}
```

### 9. Explain `keyof` operator
`keyof` creates a union type of all property names of a given type.

```typescript
interface Person {
    name: string;
    age: number;
}

type PersonKeys = keyof Person; // "name" | "age"
```

### 10. What are Utility Types?
Built-in generic types that facilitate common type transformations:
- `Partial<T>` - Makes all properties optional
- `Required<T>` - Makes all properties required
- `Readonly<T>` - Makes all properties readonly
- `Pick<T, K>` - Picks specific properties
- `Omit<T, K>` - Omits specific properties
- `Record<K, T>` - Creates object type with specified keys and values

## Advanced Questions

### 11. What is Type Inference?
TypeScript automatically infers types when you don't explicitly specify them.

```typescript
let x = 3; // x is inferred as number
let arr = [1, 2, 3]; // arr is inferred as number[]
```

### 12. Explain Conditional Types
Conditional types select one of two possible types based on a condition.

```typescript
type IsString<T> = T extends string ? true : false;
```

### 13. What is `never` type used for?
- Functions that never return (throw errors or infinite loops)
- Exhaustive type checking
- Representing impossible states

```typescript
function throwError(message: string): never {
    throw new Error(message);
}
```

### 14. What are Mapped Types?
Mapped types transform properties of an existing type.

```typescript
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};
```

### 15. Explain the `infer` keyword
`infer` is used in conditional types to infer types within the condition.

```typescript
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;
```

### 16. What are Declaration Files (.d.ts)?
Declaration files contain type information for JavaScript libraries. They provide type definitions without implementation.

### 17. What is the difference between `unknown` and `any`?
- `any`: Disables type checking
- `unknown`: Type-safe version of any; requires type checking before use

### 18. Explain Abstract Classes
Abstract classes cannot be instantiated directly and serve as base classes.

```typescript
abstract class Animal {
    abstract makeSound(): void;
    
    move(): void {
        console.log("Moving...");
    }
}
```

### 19. What are Decorators?
Decorators are special declarations that can be attached to classes, methods, properties, or parameters to modify their behavior.

```typescript
function logged(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    const original = descriptor.value;
    descriptor.value = function(...args: any[]) {
        console.log(`Calling ${propertyKey}`);
        return original.apply(this, args);
    };
}
```

### 20. What is Type Assertion?
Type assertion tells TypeScript to treat a value as a specific type.

```typescript
let someValue: unknown = "this is a string";
let strLength: number = (someValue as string).length;
// or
let strLength2: number = (<string>someValue).length;
```

## Configuration

### 21. Important tsconfig.json options
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "sourceMap": true,
    "declaration": true
  }
}
```

### 22. What does `strict` mode enable?
- `noImplicitAny`
- `strictNullChecks`
- `strictFunctionTypes`
- `strictBindCallApply`
- `strictPropertyInitialization`
- `noImplicitThis`
- `alwaysStrict`

## Best Practices

### 23. TypeScript Best Practices
- Use strict mode
- Avoid `any` type when possible
- Use interfaces for public APIs
- Leverage type inference
- Use const assertions for literal types
- Prefer union types over enums for simple cases
- Use utility types to avoid repetition
- Enable all strict flags in tsconfig.json
