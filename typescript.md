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

### 3. What are the Data Types in TypeScript?

**Answer:**

TypeScript provides a rich type system that includes JavaScript's primitive types plus additional types for better type safety and code quality. Understanding these types is fundamental to writing TypeScript effectively.

---

#### Primitive Types

**1. number - Numeric Values:**

```typescript
// All numbers (integers and floating-point)
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
let big: number = 1_000_000; // Numeric separators (ES2021)

// Special numeric values
let notANumber: number = NaN;
let infinity: number = Infinity;
let negInfinity: number = -Infinity;

// Arithmetic
let sum: number = 10 + 5; // 15
let product: number = 4 * 2.5; // 10
```

**2. string - Text Values:**

```typescript
// String literals
let firstName: string = "John";
let lastName: string = 'Doe';

// Template literals
let fullName: string = `${firstName} ${lastName}`; // "John Doe"

// Multi-line strings
let description: string = `
  This is a multi-line
  string in TypeScript
`;

// Unicode and escape sequences
let unicode: string = "\u{1F600}"; // 😀
let newline: string = "Line 1\nLine 2";
```

**3. boolean - True/False Values:**

```typescript
let isActive: boolean = true;
let isComplete: boolean = false;

// Boolean expressions
let isAdult: boolean = age >= 18;
let hasPermission: boolean = isAdmin || isModerator;

// Not null/undefined (always explicit boolean)
let isDone: boolean = Boolean(someValue); // Explicit conversion
```

**4. bigint - Large Integers (ES2020+):**

```typescript
// For numbers larger than Number.MAX_SAFE_INTEGER
let big1: bigint = 100n;
let big2: bigint = BigInt("9007199254740991");

// BigInt arithmetic
let sum: bigint = 100n + 200n; // 300n
let product: bigint = 2n * 3n; // 6n

// Cannot mix with number
// let invalid = 100n + 50; // ❌ Error

// Safe integer limits
let max: bigint = BigInt(Number.MAX_SAFE_INTEGER); // 9007199254740991n
```

**5. symbol - Unique Identifiers:**

```typescript
// Create unique symbols
let sym1: symbol = Symbol();
let sym2: symbol = Symbol("key");

// Symbols are always unique
console.log(sym1 === sym2); // false

// Use as object keys
const SECRET_KEY: symbol = Symbol("secret");
let obj = {
  [SECRET_KEY]: "hidden value"
};

// Well-known symbols
let iterator: symbol = Symbol.iterator;
```

---

#### Special Types

**1. any - Disables Type Checking:**

```typescript
// Can be anything (avoid when possible!)
let anything: any = 4;
anything = "string"; // ✅ OK
anything = true; // ✅ OK
anything.toUpperCase(); // ✅ No error (but might crash at runtime!)

// Useful for migration from JavaScript
let legacy: any = getLegacyValue();

// ❌ BAD: Overuse defeats TypeScript's purpose
let data: any; // Avoid!

// ✅ GOOD: Use specific types or unknown
let data: string | number | boolean;
```

**2. unknown - Type-Safe any:**

```typescript
// Like any, but requires type checking
let value: unknown = 4;

// ❌ Cannot use directly
// value.toFixed(2); // Error!

// ✅ Must check type first
if (typeof value === "number") {
  console.log(value.toFixed(2)); // OK
}

// Type guards with unknown
function processValue(value: unknown) {
  if (typeof value === "string") {
    return value.toUpperCase();
  }
  if (typeof value === "number") {
    return value.toFixed(2);
  }
  throw new Error("Unsupported type");
}

// Always prefer unknown over any
function safeJSON(text: string): unknown {
  return JSON.parse(text); // Don't assume the shape!
}
```

**3. void - No Return Value:**

```typescript
// Functions that don't return anything
function logMessage(message: string): void {
  console.log(message);
  // No return or return undefined
}

// Void variables (rarely used)
let unusable: void = undefined;

// Callbacks with no return
type VoidCallback = () => void;
const onClick: VoidCallback = () => {
  console.log("Clicked");
};

// Note: void functions can return undefined
function example(): void {
  return undefined; // ✅ OK
  // return null; // ❌ Error
}
```

**4. never - Unreachable Code:**

```typescript
// Function that never returns
function throwError(message: string): never {
  throw new Error(message);
}

function infiniteLoop(): never {
  while (true) {
    // Loop forever
  }
}

// Exhaustive type checking
type Shape = Circle | Square | Triangle;

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.size ** 2;
    case "triangle":
      return (shape.base * shape.height) / 2;
    default:
      // If we add a new shape and forget to handle it:
      const exhaustive: never = shape; // ❌ Error if not exhaustive!
      throw new Error(`Unhandled shape: ${exhaustive}`);
  }
}

// Impossible intersections
type Impossible = string & number; // never (can't be both)
```

**5. null and undefined:**

```typescript
// Null - intentional absence of value
let empty: null = null;

// Undefined - not initialized
let notSet: undefined = undefined;

// In strict mode, these are distinct types
let nullable: string | null = null;
let optional: string | undefined = undefined;

// Optional chaining
let length = nullable?.length; // number | undefined

// Nullish coalescing
let value = nullable ?? "default"; // Use default if null/undefined

// strictNullChecks (tsconfig.json)
// When enabled, null and undefined are NOT assignable to other types
let str: string;
// str = null; // ❌ Error with strictNullChecks
// str = undefined; // ❌ Error with strictNullChecks
```

---

#### Object Types

**1. object - Non-Primitive Type:**

```typescript
// Represents any non-primitive value
let obj: object = { name: "John" };
obj = [1, 2, 3]; // ✅ Arrays are objects
obj = new Date(); // ✅ Date is an object
obj = () => {}; // ✅ Functions are objects

// ❌ Cannot be primitives
// obj = 42; // Error
// obj = "string"; // Error

// Usually too generic - prefer specific object types
let specific: { name: string; age: number } = {
  name: "Alice",
  age: 30
};
```

**2. Object Literal Types:**

```typescript
// Define object shape inline
let user: { name: string; age: number; email?: string } = {
  name: "John",
  age: 30
};

// Optional properties (?)
let config: { host: string; port?: number } = {
  host: "localhost"
  // port is optional
};

// Readonly properties
let point: { readonly x: number; readonly y: number } = {
  x: 10,
  y: 20
};
// point.x = 5; // ❌ Error: readonly

// Index signatures
let scores: { [student: string]: number } = {
  "Alice": 95,
  "Bob": 87
};
scores["Charlie"] = 92; // ✅ OK
```

**3. Array Types:**

```typescript
// Array notation
let numbers: number[] = [1, 2, 3, 4, 5];
let strings: string[] = ["a", "b", "c"];

// Generic array type
let items: Array<number> = [1, 2, 3];

// Mixed arrays
let mixed: (string | number)[] = [1, "two", 3, "four"];

// Multi-dimensional arrays
let matrix: number[][] = [
  [1, 2, 3],
  [4, 5, 6]
];

// Readonly arrays
let immutable: readonly number[] = [1, 2, 3];
// immutable.push(4); // ❌ Error
// immutable[0] = 10; // ❌ Error

// Array methods preserve type
let doubled: number[] = numbers.map(n => n * 2);
```

**4. Tuple Types - Fixed-Length Arrays:**

```typescript
// Tuple with specific types at specific positions
let person: [string, number] = ["Alice", 30];

// Accessing elements
let name: string = person[0]; // "Alice"
let age: number = person[1]; // 30

// Destructuring
let [firstName, userAge] = person;

// Optional elements
let optionalTuple: [string, number?] = ["Bob"];

// Rest elements
let restTuple: [string, ...number[]] = ["scores", 95, 87, 92];

// Readonly tuples
let immutableTuple: readonly [number, number] = [10, 20];

// Labeled tuples (TypeScript 4.0+)
type Range = [start: number, end: number];
let range: Range = [0, 100];

// Common use: function returns
function getUser(): [string, number, boolean] {
  return ["Alice", 30, true];
}
```

**5. Enum Types - Named Constants:**

```typescript
// Numeric enum (default)
enum Direction {
  Up,    // 0
  Down,  // 1
  Left,  // 2
  Right  // 3
}

let dir: Direction = Direction.Up;

// Custom numeric values
enum Status {
  Pending = 1,
  Active = 2,
  Inactive = 3
}

// String enum (recommended)
enum Color {
  Red = "RED",
  Green = "GREEN",
  Blue = "BLUE"
}

let color: Color = Color.Red;

// Heterogeneous enum (mixed)
enum Mixed {
  No = 0,
  Yes = "YES"
}

// Const enum (compile-time only, more efficient)
const enum HttpStatus {
  OK = 200,
  NotFound = 404,
  ServerError = 500
}

// Reverse mapping (numeric enums only)
enum Role {
  Admin = 1,
  User = 2
}
let roleName: string = Role[1]; // "Admin"

// Computed members
enum FileAccess {
  None = 0,
  Read = 1 << 0,    // 1
  Write = 1 << 1,   // 2
  ReadWrite = Read | Write // 3
}
```

---

#### Function Types

**1. Function Type Expressions:**

```typescript
// Function type
let add: (a: number, b: number) => number;

add = function(x, y) {
  return x + y;
};

// Arrow function type
let multiply: (a: number, b: number) => number = (x, y) => x * y;

// Optional parameters
let greet: (name: string, greeting?: string) => string;
greet = (name, greeting = "Hello") => `${greeting}, ${name}!`;

// Rest parameters
let sum: (...numbers: number[]) => number;
sum = (...nums) => nums.reduce((a, b) => a + b, 0);

// Void return
let log: (message: string) => void;
log = (msg) => console.log(msg);
```

**2. Call Signatures:**

```typescript
// Typing an object with call signature
type DescribableFunction = {
  description: string;
  (someArg: number): boolean; // Call signature
};

function doSomething(fn: DescribableFunction) {
  console.log(fn.description);
  return fn(6);
}

// Construct signatures
type SomeConstructor = {
  new (s: string): object;
};

function create(ctor: SomeConstructor) {
  return new ctor("hello");
}
```

---

#### Literal Types

**1. String Literal Types:**

```typescript
// Only specific strings allowed
let direction: "north" | "south" | "east" | "west";
direction = "north"; // ✅ OK
// direction = "up"; // ❌ Error

// Useful for configuration
type LogLevel = "debug" | "info" | "warn" | "error";

function log(level: LogLevel, message: string) {
  console.log(`[${level}] ${message}`);
}

log("info", "Server started"); // ✅ OK
// log("verbose", "Details"); // ❌ Error
```

**2. Numeric Literal Types:**

```typescript
// Only specific numbers
type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;

function rollDice(): DiceRoll {
  return (Math.floor(Math.random() * 6) + 1) as DiceRoll;
}

// HTTP status codes
type SuccessStatus = 200 | 201 | 204;
type ErrorStatus = 400 | 401 | 403 | 404 | 500;
```

**3. Boolean Literal Types:**

```typescript
// Usually not needed, but possible
type AlwaysTrue = true;
type AlwaysFalse = false;

// More useful in discriminated unions
type Response =
  | { success: true; data: any }
  | { success: false; error: string };

function handleResponse(response: Response) {
  if (response.success) {
    console.log(response.data); // TypeScript knows this branch
  } else {
    console.error(response.error); // TypeScript knows this branch
  }
}
```

**4. Template Literal Types (TypeScript 4.1+):**

```typescript
// Combine string literals
type Color = "red" | "blue";
type Size = "small" | "large";
type Style = `${Color}-${Size}`; // "red-small" | "red-large" | "blue-small" | "blue-large"

// Event names
type PropEventSource<Type> = {
  on<Key extends string & keyof Type>(
    eventName: `${Key}Changed`,
    callback: (newValue: Type[Key]) => void
  ): void;
};

// CSS units
type CSSUnit = "px" | "em" | "rem" | "%";
type Size = `${number}${CSSUnit}`;
let width: Size = "100px"; // ✅ OK
// let invalid: Size = "100"; // ❌ Error
```

---

#### Union and Intersection Types

**1. Union Types (OR):**

```typescript
// Can be one of several types
let value: string | number;
value = "hello"; // ✅ OK
value = 42; // ✅ OK
// value = true; // ❌ Error

// Union with null/undefined
let nullable: string | null = null;

// Multiple unions
type ID = string | number;
type Response = Success | Error | Pending;

// Type narrowing with unions
function printValue(value: string | number) {
  if (typeof value === "string") {
    console.log(value.toUpperCase()); // String methods
  } else {
    console.log(value.toFixed(2)); // Number methods
  }
}

// Array of union
let mixed: (string | number)[] = [1, "two", 3];
```

**2. Intersection Types (AND):**

```typescript
// Combine multiple types
type Person = { name: string; age: number };
type Employee = { employeeId: number; department: string };
type EmployeePerson = Person & Employee;

let employee: EmployeePerson = {
  name: "John",
  age: 30,
  employeeId: 12345,
  department: "IT"
};

// Intersection with functions
type Loggable = { log: () => void };
type Serializable = { serialize: () => string };
type LoggableSerializable = Loggable & Serializable;

// Primitive intersections result in never
type Impossible = string & number; // never
```

---

#### Advanced Type Features

**1. Type Aliases:**

```typescript
// Create custom type names
type Point = { x: number; y: number };
type ID = string | number;
type Callback = (data: string) => void;

// Generic type aliases
type Container<T> = { value: T };
let stringContainer: Container<string> = { value: "hello" };

// Recursive types
type TreeNode<T> = {
  value: T;
  left?: TreeNode<T>;
  right?: TreeNode<T>;
};
```

**2. Interfaces:**

```typescript
// Define object shapes (can be extended)
interface User {
  id: number;
  name: string;
  email: string;
}

// Optional properties
interface Config {
  host: string;
  port?: number;
}

// Readonly properties
interface Point {
  readonly x: number;
  readonly y: number;
}

// Function properties
interface Calculator {
  add(a: number, b: number): number;
  subtract(a: number, b: number): number;
}

// Index signatures
interface StringMap {
  [key: string]: string;
}

// Extending interfaces
interface Admin extends User {
  permissions: string[];
}
```

**3. Generics:**

```typescript
// Generic functions
function identity<T>(arg: T): T {
  return arg;
}

let output = identity<string>("hello");
let inferredOutput = identity(42); // Type inferred as number

// Generic interfaces
interface Box<T> {
  value: T;
}

let stringBox: Box<string> = { value: "hello" };
let numberBox: Box<number> = { value: 42 };

// Generic constraints
function getLength<T extends { length: number }>(arg: T): number {
  return arg.length; // ✅ OK because T has length
}

getLength("hello"); // ✅ String has length
getLength([1, 2, 3]); // ✅ Array has length
// getLength(42); // ❌ Number doesn't have length

// Multiple type parameters
function pair<T, U>(first: T, second: U): [T, U] {
  return [first, second];
}

let result = pair("hello", 42); // [string, number]
```

---

#### Type Assertions

**1. as Syntax:**

```typescript
// Tell TypeScript: "Trust me, I know the type"
let someValue: unknown = "this is a string";
let strLength: number = (someValue as string).length;

// DOM element types
let input = document.getElementById("myInput") as HTMLInputElement;
input.value = "Hello";

// Const assertions
let obj = { x: 10, y: 20 } as const;
// obj.x = 5; // ❌ Error: readonly

let tuple = [10, 20] as const; // readonly [10, 20]
```

**2. Angle-Bracket Syntax (JSX conflicts):**

```typescript
// Alternative syntax (not usable in .tsx files)
let someValue: unknown = "string";
let strLength: number = (<string>someValue).length;
```

**3. Non-null Assertion (!):**

```typescript
// Tell TypeScript: "This won't be null/undefined"
function processValue(value: string | null) {
  // You're certain value is not null here
  console.log(value!.toUpperCase());
}

// DOM elements
let element = document.getElementById("myDiv")!;
element.innerHTML = "Hello"; // No null check

// Use sparingly - can hide bugs!
```

---

#### Type Narrowing

**1. typeof Guards:**

```typescript
function printValue(value: string | number) {
  if (typeof value === "string") {
    console.log(value.toUpperCase());
  } else {
    console.log(value.toFixed(2));
  }
}
```

**2. instanceof Guards:**

```typescript
function processValue(value: Date | string) {
  if (value instanceof Date) {
    console.log(value.toISOString());
  } else {
    console.log(value.toUpperCase());
  }
}
```

**3. in Operator:**

```typescript
type Fish = { swim: () => void };
type Bird = { fly: () => void };

function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    animal.swim();
  } else {
    animal.fly();
  }
}
```

**4. Discriminated Unions:**

```typescript
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; size: number }
  | { kind: "rectangle"; width: number; height: number };

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.size ** 2;
    case "rectangle":
      return shape.width * shape.height;
  }
}
```

---

#### Summary

**Quick Reference - All TypeScript Data Types:**

```typescript
// Primitives
let num: number = 42;
let str: string = "hello";
let bool: boolean = true;
let big: bigint = 100n;
let sym: symbol = Symbol("key");

// Special Types
let any: any = anything;
let unknown: unknown = anything;
let none: void = undefined;
let never: never = throwError();
let nul: null = null;
let undef: undefined = undefined;

// Object Types
let obj: object = {};
let arr: number[] = [1, 2, 3];
let tuple: [string, number] = ["a", 1];
enum Color { Red, Green, Blue }

// Function Types
let func: (x: number) => number;

// Literal Types
let literal: "exact" | 42 | true;

// Unions & Intersections
let union: string | number;
let intersection: Type1 & Type2;

// Advanced
type Alias = string;
interface Interface { prop: string }
type Generic<T> = T[];
```

**Key Principles:**
- ✅ Use specific types over `any`
- ✅ Enable `strict` mode in tsconfig.json
- ✅ Use `unknown` instead of `any` when type is truly unknown
- ✅ Leverage type inference where possible
- ✅ Use literal types for specific values
- ✅ Use union types for multiple possibilities
- ✅ Use readonly for immutability
- ✅ Use const assertions for literal types
- ✅ Always handle null/undefined in strict mode
- ✅ Use type narrowing to refine types

### 4. What are Type Aliases and How to Create Them in TypeScript?

**Answer:**

Type aliases allow you to create custom names for types, making code more readable, reusable, and maintainable. They use the `type` keyword to define a new name for any type.

---

#### What are Type Aliases?

**Definition:**
A type alias is a new name for an existing type. It doesn't create a new type; it simply provides a convenient reference to an existing type.

**Syntax:**

```typescript
type AliasName = ExistingType;
```

**Basic Example:**

```typescript
// Without type alias
let userId: string | number;
let postId: string | number;
let commentId: string | number;

// With type alias
type ID = string | number;
let userId: ID;
let postId: ID;
let commentId: ID;
```

---

#### Creating Type Aliases

**1. Primitive Type Aliases:**

```typescript
// Simple primitive aliases
type Username = string;
type Age = number;
type IsActive = boolean;

let user: Username = "alice";
let userAge: Age = 30;
let active: IsActive = true;

// More descriptive than just using primitives
type Email = string;
type Password = string;

function login(email: Email, password: Password): boolean {
  // Implementation
  return true;
}
```

**2. Union Type Aliases:**

```typescript
// Union of primitives
type Status = "pending" | "active" | "inactive" | "suspended";
type ID = string | number;

let orderStatus: Status = "pending";
let userId: ID = 123;
userId = "abc-123"; // ✅ Also OK

// Union of types
type Result = string | Error;

function processData(): Result {
  if (Math.random() > 0.5) {
    return "Success";
  }
  return new Error("Failed");
}
```

**3. Object Type Aliases:**

```typescript
// Define object shape
type Point = {
  x: number;
  y: number;
};

let position: Point = { x: 10, y: 20 };

// Complex object
type User = {
  id: number;
  name: string;
  email: string;
  age?: number; // Optional property
  readonly createdAt: Date; // Readonly property
};

const user: User = {
  id: 1,
  name: "Alice",
  email: "alice@example.com",
  createdAt: new Date()
};

// user.createdAt = new Date(); // ❌ Error: readonly property
```

**4. Function Type Aliases:**

```typescript
// Function signature
type Callback = (data: string) => void;
type MathOperation = (a: number, b: number) => number;

const handleData: Callback = (data) => {
  console.log(data);
};

const add: MathOperation = (a, b) => a + b;
const multiply: MathOperation = (a, b) => a * b;

// With optional parameters
type Logger = (message: string, level?: string) => void;

const log: Logger = (message, level = "info") => {
  console.log(`[${level}] ${message}`);
};

// Async function
type AsyncFetcher = (url: string) => Promise<any>;

const fetchData: AsyncFetcher = async (url) => {
  const response = await fetch(url);
  return response.json();
};
```

**5. Array Type Aliases:**

```typescript
// Array of primitives
type StringArray = string[];
type NumberArray = number[];

let names: StringArray = ["Alice", "Bob", "Charlie"];
let scores: NumberArray = [95, 87, 92];

// Array of objects
type UserList = User[];
type PointArray = Point[];

// Tuple type alias
type Coordinate = [number, number];
type RGB = [number, number, number];

let point: Coordinate = [10, 20];
let color: RGB = [255, 128, 0];

// Complex tuple
type Response = [boolean, string, number];
const result: Response = [true, "Success", 200];
```

**6. Intersection Type Aliases:**

```typescript
// Combine multiple types
type Person = {
  name: string;
  age: number;
};

type Employee = {
  employeeId: string;
  department: string;
};

type Staff = Person & Employee;

const employee: Staff = {
  name: "John",
  age: 30,
  employeeId: "E123",
  department: "IT"
};

// Intersection with additional properties
type Timestamped = {
  createdAt: Date;
  updatedAt: Date;
};

type Product = {
  id: string;
  name: string;
  price: number;
};

type TrackedProduct = Product & Timestamped;
```

---

#### Generic Type Aliases

**1. Simple Generic Alias:**

```typescript
// Container type
type Container<T> = {
  value: T;
};

let stringContainer: Container<string> = { value: "hello" };
let numberContainer: Container<number> = { value: 42 };

// Array wrapper
type List<T> = T[];
let names: List<string> = ["Alice", "Bob"];
let scores: List<number> = [95, 87];
```

**2. Multiple Type Parameters:**

```typescript
// Key-value pair
type Pair<K, V> = {
  key: K;
  value: V;
};

let setting: Pair<string, number> = {
  key: "timeout",
  value: 5000
};

// Result with data and error
type Result<T, E> = {
  data?: T;
  error?: E;
};

let apiResult: Result<User, string> = {
  data: { id: 1, name: "Alice", email: "alice@example.com", createdAt: new Date() }
};
```

**3. Generic with Constraints:**

```typescript
// Constrain to objects with id
type WithId<T extends { id: number }> = T;

type ValidUser = WithId<{ id: number; name: string }>; // ✅ OK
// type InvalidUser = WithId<{ name: string }>; // ❌ Error: missing id

// Constrain to specific types
type Nullable<T> = T | null;
type Optional<T> = T | undefined;

let name: Nullable<string> = null; // ✅ OK
let age: Optional<number> = undefined; // ✅ OK

// Array element type
type ArrayElement<T extends any[]> = T[number];

type Numbers = number[];
type Num = ArrayElement<Numbers>; // number

type StringOrNumber = (string | number)[];
type Item = ArrayElement<StringOrNumber>; // string | number
```

**4. Generic Function Types:**

```typescript
// Generic mapper
type Mapper<T, U> = (item: T) => U;

const toString: Mapper<number, string> = (num) => num.toString();
const double: Mapper<number, number> = (num) => num * 2;

// Generic predicate
type Predicate<T> = (item: T) => boolean;

const isEven: Predicate<number> = (num) => num % 2 === 0;
const isLongString: Predicate<string> = (str) => str.length > 10;

// Generic promise
type AsyncMapper<T, U> = (item: T) => Promise<U>;

const fetchUser: AsyncMapper<number, User> = async (id) => {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
};
```

---

#### Advanced Type Alias Patterns

**1. Recursive Type Aliases:**

```typescript
// Tree structure
type TreeNode<T> = {
  value: T;
  children?: TreeNode<T>[];
};

const fileTree: TreeNode<string> = {
  value: "root",
  children: [
    { value: "src", children: [{ value: "index.ts" }] },
    { value: "package.json" }
  ]
};

// Linked list
type LinkedListNode<T> = {
  value: T;
  next?: LinkedListNode<T>;
};

// JSON value
type JSONValue = 
  | string
  | number
  | boolean
  | null
  | JSONValue[]
  | { [key: string]: JSONValue };

const data: JSONValue = {
  name: "Alice",
  age: 30,
  active: true,
  tags: ["admin", "user"],
  meta: { created: "2024-01-01" }
};
```

**2. Conditional Type Aliases:**

```typescript
// Extract type based on condition
type IsArray<T> = T extends any[] ? true : false;

type Test1 = IsArray<string[]>; // true
type Test2 = IsArray<number>;   // false

// Unwrap promise
type Awaited<T> = T extends Promise<infer U> ? U : T;

type AsyncString = Awaited<Promise<string>>; // string
type SyncNumber = Awaited<number>;           // number

// Extract return type
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type Fn = (x: number) => string;
type FnReturn = ReturnType<Fn>; // string
```

**3. Mapped Type Aliases:**

```typescript
// Make all properties optional
type Partial<T> = {
  [P in keyof T]?: T[P];
};

type User = { id: number; name: string; email: string };
type PartialUser = Partial<User>;
// { id?: number; name?: string; email?: string }

// Make all properties readonly
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

type ImmutableUser = Readonly<User>;
// { readonly id: number; readonly name: string; readonly email: string }

// Pick specific properties
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];
};

type UserPreview = Pick<User, "id" | "name">;
// { id: number; name: string }
```

**4. Template Literal Type Aliases:**

```typescript
// Event names
type EventName = "click" | "focus" | "blur";
type EventHandler = `on${Capitalize<EventName>}`;
// "onClick" | "onFocus" | "onBlur"

// CSS properties
type Size = "small" | "medium" | "large";
type Color = "red" | "green" | "blue";
type ClassName = `${Color}-${Size}`;
// "red-small" | "red-medium" | ... (9 combinations)

// HTTP methods with paths
type Method = "GET" | "POST" | "PUT" | "DELETE";
type Endpoint = `/${string}`;
type Route = `${Method} ${Endpoint}`;

const route: Route = "GET /api/users"; // ✅ OK
```

---

#### Real-World Examples

**1. API Response Types:**

```typescript
// Generic API response
type ApiResponse<T> = {
  success: boolean;
  data: T;
  message?: string;
  errors?: string[];
};

// User API
type UserResponse = ApiResponse<User>;
type UsersResponse = ApiResponse<User[]>;

async function getUser(id: number): Promise<UserResponse> {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}

// Paginated response
type PaginatedResponse<T> = {
  items: T[];
  total: number;
  page: number;
  pageSize: number;
  hasMore: boolean;
};

type UserPage = PaginatedResponse<User>;
```

**2. Form State Types:**

```typescript
// Form field
type FormField<T> = {
  value: T;
  error?: string;
  touched: boolean;
  dirty: boolean;
};

// Form state
type FormState<T> = {
  [K in keyof T]: FormField<T[K]>;
};

// Usage
type LoginForm = {
  email: string;
  password: string;
};

type LoginFormState = FormState<LoginForm>;
// {
//   email: FormField<string>;
//   password: FormField<string>;
// }

const formState: LoginFormState = {
  email: { value: "", touched: false, dirty: false },
  password: { value: "", touched: false, dirty: false }
};
```

**3. Redux Action Types:**

```typescript
// Action type
type Action<T extends string, P = void> = P extends void
  ? { type: T }
  : { type: T; payload: P };

// Action creators
type LoginAction = Action<"USER_LOGIN", { username: string; password: string }>;
type LogoutAction = Action<"USER_LOGOUT">;
type FetchDataAction = Action<"FETCH_DATA", { endpoint: string }>;

type AppAction = LoginAction | LogoutAction | FetchDataAction;

// Reducer type
type Reducer<S, A> = (state: S, action: A) => S;

type AppState = { user: User | null };
type AppReducer = Reducer<AppState, AppAction>;
```

**4. Database Model Types:**

```typescript
// Base model
type BaseModel = {
  id: string;
  createdAt: Date;
  updatedAt: Date;
};

// Model with timestamps
type Model<T> = T & BaseModel;

// Specific models
type User = Model<{
  name: string;
  email: string;
  role: "admin" | "user";
}>;

type Post = Model<{
  title: string;
  content: string;
  authorId: string;
  published: boolean;
}>;

// Model without ID (for creation)
type NewModel<T> = Omit<Model<T>, "id" | "createdAt" | "updatedAt">;

type NewUser = NewModel<User>;
// { name: string; email: string; role: "admin" | "user" }
```

**5. Event System:**

```typescript
// Event map
type EventMap = {
  click: { x: number; y: number };
  keypress: { key: string };
  submit: { formData: Record<string, any> };
};

// Event listener
type EventListener<T> = (data: T) => void;

// Event emitter
type EventEmitter<T extends Record<string, any>> = {
  on<K extends keyof T>(event: K, listener: EventListener<T[K]>): void;
  emit<K extends keyof T>(event: K, data: T[K]): void;
};

type AppEmitter = EventEmitter<EventMap>;

const emitter: AppEmitter = {
  on(event, listener) {
    // Implementation
  },
  emit(event, data) {
    // Implementation
  }
};

// Type-safe usage
emitter.on("click", (data) => {
  console.log(data.x, data.y); // ✅ data is { x: number; y: number }
});
```

---

#### Type Aliases vs Interfaces

**When to Use Type Aliases:**

```typescript
// ✅ Use type aliases for:

// 1. Unions
type Status = "pending" | "success" | "error";

// 2. Tuples
type Point = [number, number];

// 3. Primitives
type ID = string | number;

// 4. Function types
type Callback = (data: string) => void;

// 5. Complex mapped types
type Readonly<T> = { readonly [P in keyof T]: T[P] };

// 6. Conditional types
type NonNullable<T> = T extends null | undefined ? never : T;

// 7. Template literals
type EventHandler = `on${Capitalize<string>}`;
```

**When to Use Interfaces:**

```typescript
// ✅ Use interfaces for:

// 1. Object shapes (especially when extending)
interface User {
  id: number;
  name: string;
}

// 2. Declaration merging (useful for libraries)
interface Window {
  customProperty: string;
}

interface Window {
  anotherProperty: number;
}
// Both merge into single interface

// 3. Class implementation
interface Printable {
  print(): void;
}

class Document implements Printable {
  print() { /* ... */ }
}

// 4. Extending other interfaces
interface Admin extends User {
  permissions: string[];
}
```

**Comparison:**

```typescript
// Type alias
type UserType = {
  name: string;
  age: number;
};

// Interface
interface UserInterface {
  name: string;
  age: number;
}

// Both work similarly for object shapes
// But type aliases are more flexible for unions, tuples, etc.
```

---

#### Best Practices

**1. Use Descriptive Names:**

```typescript
// ❌ BAD: Unclear names
type T = string;
type D = { x: number; y: number };

// ✅ GOOD: Clear, descriptive names
type UserId = string;
type Point2D = { x: number; y: number };
```

**2. Keep Aliases Simple:**

```typescript
// ❌ BAD: Too complex inline
function process(data: { id: string; name: string; meta: { created: Date; updated: Date } }[]) {}

// ✅ GOOD: Extract to type alias
type Metadata = {
  created: Date;
  updated: Date;
};

type Item = {
  id: string;
  name: string;
  meta: Metadata;
};

function process(data: Item[]) {}
```

**3. Use Generics for Reusability:**

```typescript
// ❌ BAD: Repetitive types
type StringResponse = { data: string; error?: string };
type NumberResponse = { data: number; error?: string };
type UserResponse = { data: User; error?: string };

// ✅ GOOD: Generic type
type Response<T> = {
  data: T;
  error?: string;
};

type StringResponse = Response<string>;
type NumberResponse = Response<number>;
type UserResponse = Response<User>;
```

**4. Document Complex Types:**

```typescript
/**
 * Represents an API response with generic data type
 * @template T - The type of data returned
 */
type ApiResponse<T> = {
  /** Indicates if request was successful */
  success: boolean;
  /** Response data if successful */
  data?: T;
  /** Error message if failed */
  error?: string;
  /** HTTP status code */
  status: number;
};
```

**5. Prefer Type Aliases for Unions:**

```typescript
// ✅ GOOD: Type alias for union
type Result = Success | Error;
type Status = "pending" | "active" | "inactive";

// ❌ Cannot do this with interface
// interface Status = "pending" | "active" | "inactive"; // Syntax error
```

---

#### Common Patterns

**1. Optional Fields Pattern:**

```typescript
// Make specific fields optional
type PartialBy<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>;

type User = {
  id: number;
  name: string;
  email: string;
};

type UserUpdate = PartialBy<User, "name" | "email">;
// { id: number; name?: string; email?: string }
```

**2. Required Fields Pattern:**

```typescript
// Make specific fields required
type RequiredBy<T, K extends keyof T> = Omit<T, K> & Required<Pick<T, K>>;

type Config = {
  host?: string;
  port?: number;
  database?: string;
};

type RequiredConfig = RequiredBy<Config, "host" | "database">;
// { host: string; port?: number; database: string }
```

**3. Exclude Fields Pattern:**

```typescript
// Exclude specific properties
type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>;

type User = {
  id: number;
  name: string;
  password: string;
  email: string;
};

type PublicUser = Omit<User, "password">;
// { id: number; name: string; email: string }
```

---

#### Summary

**Creating Type Aliases:**

```typescript
// Syntax
type AliasName = Type;

// Examples
type ID = string | number;                    // Union
type Point = { x: number; y: number };        // Object
type Callback = (data: string) => void;       // Function
type Tuple = [string, number];                // Tuple
type Container<T> = { value: T };             // Generic
type Status = "pending" | "active";           // Literal union
```

**Key Benefits:**
- 📝 Improve code readability
- 🔄 Reuse complex types
- 🎯 Create semantic meaning
- 💡 Better IDE support
- 🔧 Easier maintenance
- 🎨 More expressive code
- 📦 Modular type definitions
- ✅ Type safety

**Quick Reference:**

| Pattern | Example | Use Case |
|---------|---------|----------|
| Primitive | `type ID = string;` | Semantic naming |
| Union | `type Status = "on" \| "off";` | Limited values |
| Object | `type User = { name: string };` | Data structures |
| Function | `type Fn = (x: number) => void;` | Callbacks |
| Generic | `type Box<T> = { value: T };` | Reusable types |
| Tuple | `type Pair = [string, number];` | Fixed arrays |
| Intersection | `type A = B & C;` | Combine types |
| Conditional | `type X<T> = T extends Y ? A : B;` | Type logic |

---

### 5. What is the difference between `interface` and `type` in TypeScript?
- **Interface**: Can be extended and merged, primarily used for object shapes
- **Type**: Can represent primitive types, unions, tuples, and more complex types
- Interfaces can be reopened to add new properties (declaration merging)
- Types cannot be reopened once created

### 5.5. What are Rest Parameters in TypeScript?

**Answer:**

Rest parameters allow you to represent an indefinite number of arguments as an array. They are denoted by three dots (`...`) before the parameter name and must be the last parameter in a function.

---

#### What are Rest Parameters?

**Definition:**
Rest parameters collect multiple arguments into a single array parameter. They provide a type-safe way to handle variable numbers of arguments.

**Syntax:**

```typescript
function functionName(...paramName: Type[]): ReturnType {
  // paramName is an array of Type
}
```

**Basic Example:**

```typescript
// Without rest parameters - limited arguments
function add(a: number, b: number): number {
  return a + b;
}

// With rest parameters - unlimited arguments
function sum(...numbers: number[]): number {
  return numbers.reduce((total, num) => total + num, 0);
}

sum(1, 2, 3);          // 6
sum(1, 2, 3, 4, 5);    // 15
sum(10);               // 10
sum();                 // 0
```

---

#### Basic Rest Parameters

**1. Simple Rest Parameters:**

```typescript
// Array of numbers
function multiply(...numbers: number[]): number {
  return numbers.reduce((product, num) => product * num, 1);
}

multiply(2, 3, 4);  // 24

// Array of strings
function concatenate(...strings: string[]): string {
  return strings.join(' ');
}

concatenate("Hello", "World", "!");  // "Hello World !"

// Array of any type
function logAll(...items: any[]): void {
  items.forEach(item => console.log(item));
}

logAll("text", 42, true, { key: "value" });
```

**2. Rest Parameters with Regular Parameters:**

```typescript
// Regular parameter + rest parameters
function greet(greeting: string, ...names: string[]): string {
  return `${greeting}, ${names.join(" and ")}!`;
}

greet("Hello", "Alice");                    // "Hello, Alice!"
greet("Hello", "Alice", "Bob");             // "Hello, Alice and Bob!"
greet("Hello", "Alice", "Bob", "Charlie");  // "Hello, Alice and Bob and Charlie!"

// Multiple regular parameters + rest
function calculateTotal(tax: number, discount: number, ...prices: number[]): number {
  const subtotal = prices.reduce((sum, price) => sum + price, 0);
  return (subtotal - discount) * (1 + tax);
}

calculateTotal(0.1, 5, 100, 200, 50);  // (100 + 200 + 50 - 5) * 1.1 = 379.5
```

**3. Rest Parameters Must Be Last:**

```typescript
// ✅ CORRECT: Rest parameter is last
function process(action: string, ...items: number[]): void {
  console.log(`${action}:`, items);
}

// ❌ ERROR: Rest parameter must be last
// function invalid(...items: number[], action: string): void {
//   // Error: A rest parameter must be last in a parameter list
// }

// ❌ ERROR: Only one rest parameter allowed
// function invalid2(...items1: number[], ...items2: string[]): void {
//   // Error: A rest parameter must be last in a parameter list
// }
```

---

#### Typed Rest Parameters

**1. Union Type Rest Parameters:**

```typescript
// Mix of strings and numbers
function logValues(...values: (string | number)[]): void {
  values.forEach(value => {
    if (typeof value === "string") {
      console.log(`String: ${value}`);
    } else {
      console.log(`Number: ${value}`);
    }
  });
}

logValues("hello", 42, "world", 100);
```

**2. Object Type Rest Parameters:**

```typescript
// Array of objects
interface User {
  name: string;
  age: number;
}

function createTeam(...members: User[]): User[] {
  return members;
}

const team = createTeam(
  { name: "Alice", age: 30 },
  { name: "Bob", age: 25 },
  { name: "Charlie", age: 35 }
);

// Array of specific interface
interface Point {
  x: number;
  y: number;
}

function calculateDistance(...points: Point[]): number[] {
  const distances: number[] = [];
  for (let i = 1; i < points.length; i++) {
    const dx = points[i].x - points[i - 1].x;
    const dy = points[i].y - points[i - 1].y;
    distances.push(Math.sqrt(dx * dx + dy * dy));
  }
  return distances;
}
```

**3. Generic Rest Parameters:**

```typescript
// Generic type for rest parameters
function merge<T>(...objects: T[]): T {
  return Object.assign({}, ...objects);
}

const merged = merge({ a: 1 }, { b: 2 }, { c: 3 });
// { a: 1, b: 2, c: 3 }

// Generic with constraint
function findMax<T extends number | string>(...values: T[]): T {
  return values.reduce((max, current) => current > max ? current : max);
}

findMax(1, 5, 3, 9, 2);      // 9
findMax("apple", "banana");   // "banana"
```

---

#### Rest Parameters with Tuples

**1. Rest Tuple Parameters:**

```typescript
// Rest parameter as tuple type
function coordinates(...coords: [number, number][]): string {
  return coords.map(([x, y]) => `(${x}, ${y})`).join(", ");
}

coordinates([0, 0], [10, 20], [30, 40]);
// "(0, 0), (10, 20), (30, 40)"

// Exact tuple types
function createPerson(...args: [string, number, boolean]): object {
  const [name, age, active] = args;
  return { name, age, active };
}

createPerson("Alice", 30, true);
// { name: "Alice", age: 30, active: true }
```

**2. Mixed Tuple and Rest:**

```typescript
// Tuple followed by rest
function process(
  first: string,
  second: number,
  ...rest: boolean[]
): void {
  console.log({ first, second, rest });
}

process("hello", 42, true, false, true);
// { first: "hello", second: 42, rest: [true, false, true] }
```

---

#### Rest Parameters in Arrow Functions

**1. Arrow Function Rest Parameters:**

```typescript
// Simple arrow function with rest
const sum = (...numbers: number[]): number => {
  return numbers.reduce((a, b) => a + b, 0);
};

// Concise arrow function
const max = (...numbers: number[]): number => 
  Math.max(...numbers);

// With type inference
const join = (...strings: string[]) => strings.join(", ");

// Arrow function as callback
const numbers = [1, 2, 3, 4, 5];
const result = numbers.reduce((acc, ...rest) => {
  // rest is empty array here (not a rest parameter context)
  return acc;
});
```

**2. Arrow Functions with Regular and Rest Parameters:**

```typescript
// Mixed parameters
const greetAll = (greeting: string, ...names: string[]): string => {
  return `${greeting} to ${names.join(", ")}`;
};

greetAll("Welcome", "Alice", "Bob", "Charlie");
// "Welcome to Alice, Bob, Charlie"

// With destructuring
const printUser = (title: string, ...userData: [string, number]): void => {
  const [name, age] = userData;
  console.log(`${title}: ${name} (${age} years old)`);
};

printUser("Employee", "Alice", 30);
```

---

#### Rest Parameters with Type Guards

**1. Filtering with Type Guards:**

```typescript
// Filter different types
function processValues(...values: (string | number | boolean)[]): object {
  const strings = values.filter((v): v is string => typeof v === "string");
  const numbers = values.filter((v): v is number => typeof v === "number");
  const booleans = values.filter((v): v is boolean => typeof v === "boolean");
  
  return { strings, numbers, booleans };
}

processValues("hello", 42, true, "world", 100, false);
// { 
//   strings: ["hello", "world"],
//   numbers: [42, 100],
//   booleans: [true, false]
// }
```

**2. Type-Safe Operations:**

```typescript
// Ensure all values are of specific type
function sumIfNumbers(...values: (string | number)[]): number {
  return values
    .filter((v): v is number => typeof v === "number")
    .reduce((sum, num) => sum + num, 0);
}

sumIfNumbers(10, "hello", 20, "world", 30);  // 60
```

---

#### Rest Parameters in Class Methods

**1. Instance Methods:**

```typescript
class Calculator {
  private history: number[] = [];
  
  // Method with rest parameters
  add(...numbers: number[]): number {
    const sum = numbers.reduce((a, b) => a + b, 0);
    this.history.push(sum);
    return sum;
  }
  
  multiply(...numbers: number[]): number {
    const product = numbers.reduce((a, b) => a * b, 1);
    this.history.push(product);
    return product;
  }
  
  getHistory(): number[] {
    return [...this.history];
  }
}

const calc = new Calculator();
calc.add(1, 2, 3);       // 6
calc.multiply(2, 3, 4);  // 24
calc.getHistory();       // [6, 24]
```

**2. Static Methods:**

```typescript
class ArrayUtils {
  // Static method with rest parameters
  static merge<T>(...arrays: T[][]): T[] {
    return arrays.flat();
  }
  
  static unique<T>(...values: T[]): T[] {
    return [...new Set(values)];
  }
  
  static min(...numbers: number[]): number {
    return Math.min(...numbers);
  }
}

ArrayUtils.merge([1, 2], [3, 4], [5, 6]);  // [1, 2, 3, 4, 5, 6]
ArrayUtils.unique(1, 2, 2, 3, 3, 4);       // [1, 2, 3, 4]
ArrayUtils.min(5, 2, 8, 1, 9);             // 1
```

**3. Constructor with Rest Parameters:**

```typescript
class Team {
  members: string[];
  
  constructor(leader: string, ...members: string[]) {
    this.members = [leader, ...members];
  }
  
  addMembers(...newMembers: string[]): void {
    this.members.push(...newMembers);
  }
  
  listMembers(): string {
    return this.members.join(", ");
  }
}

const team = new Team("Alice", "Bob", "Charlie");
team.addMembers("David", "Eve");
team.listMembers();  // "Alice, Bob, Charlie, David, Eve"
```

---

#### Real-World Patterns

**1. Logger Implementation:**

```typescript
enum LogLevel {
  DEBUG = 0,
  INFO = 1,
  WARN = 2,
  ERROR = 3
}

class Logger {
  private minLevel: LogLevel;
  
  constructor(minLevel: LogLevel = LogLevel.INFO) {
    this.minLevel = minLevel;
  }
  
  private log(level: LogLevel, ...messages: any[]): void {
    if (level >= this.minLevel) {
      const levelName = LogLevel[level];
      const timestamp = new Date().toISOString();
      console.log(`[${timestamp}] [${levelName}]`, ...messages);
    }
  }
  
  debug(...messages: any[]): void {
    this.log(LogLevel.DEBUG, ...messages);
  }
  
  info(...messages: any[]): void {
    this.log(LogLevel.INFO, ...messages);
  }
  
  warn(...messages: any[]): void {
    this.log(LogLevel.WARN, ...messages);
  }
  
  error(...messages: any[]): void {
    this.log(LogLevel.ERROR, ...messages);
  }
}

const logger = new Logger(LogLevel.DEBUG);
logger.info("User logged in", { userId: 123 });
logger.error("Failed to fetch data", new Error("Network error"));
```

**2. Event Emitter:**

```typescript
type EventHandler<T = any> = (...args: T[]) => void;

class EventEmitter<T extends Record<string, any>> {
  private events: Map<keyof T, EventHandler[]> = new Map();
  
  on<K extends keyof T>(event: K, handler: EventHandler<T[K]>): void {
    if (!this.events.has(event)) {
      this.events.set(event, []);
    }
    this.events.get(event)!.push(handler);
  }
  
  emit<K extends keyof T>(event: K, ...args: T[K][]): void {
    const handlers = this.events.get(event);
    if (handlers) {
      handlers.forEach(handler => handler(...args));
    }
  }
  
  off<K extends keyof T>(event: K, handler: EventHandler<T[K]>): void {
    const handlers = this.events.get(event);
    if (handlers) {
      const index = handlers.indexOf(handler);
      if (index > -1) {
        handlers.splice(index, 1);
      }
    }
  }
}

// Usage
interface AppEvents {
  userLogin: [userId: string, timestamp: Date];
  dataUpdate: [data: any];
  error: [error: Error];
}

const emitter = new EventEmitter<AppEvents>();

emitter.on("userLogin", (userId, timestamp) => {
  console.log(`User ${userId} logged in at ${timestamp}`);
});

emitter.emit("userLogin", "user123", new Date());
```

**3. Validation Functions:**

```typescript
interface ValidationRule<T> {
  validate: (value: T) => boolean;
  message: string;
}

function validateAll<T>(
  value: T,
  ...rules: ValidationRule<T>[]
): { valid: boolean; errors: string[] } {
  const errors: string[] = [];
  
  for (const rule of rules) {
    if (!rule.validate(value)) {
      errors.push(rule.message);
    }
  }
  
  return {
    valid: errors.length === 0,
    errors
  };
}

// Validation rules
const minLength = (min: number): ValidationRule<string> => ({
  validate: (value) => value.length >= min,
  message: `Must be at least ${min} characters`
});

const maxLength = (max: number): ValidationRule<string> => ({
  validate: (value) => value.length <= max,
  message: `Must be at most ${max} characters`
});

const hasUpperCase: ValidationRule<string> = {
  validate: (value) => /[A-Z]/.test(value),
  message: "Must contain at least one uppercase letter"
};

// Usage
const result = validateAll(
  "password",
  minLength(8),
  maxLength(20),
  hasUpperCase
);

console.log(result);
// { valid: false, errors: ["Must contain at least one uppercase letter"] }
```

**4. Pipeline/Compose Functions:**

```typescript
// Function composition with rest parameters
type Func<T = any> = (arg: T) => T;

function compose<T>(...functions: Func<T>[]): Func<T> {
  return (initialValue: T) => {
    return functions.reduceRight((value, fn) => fn(value), initialValue);
  };
}

function pipe<T>(...functions: Func<T>[]): Func<T> {
  return (initialValue: T) => {
    return functions.reduce((value, fn) => fn(value), initialValue);
  };
}

// Usage
const addOne = (x: number) => x + 1;
const double = (x: number) => x * 2;
const square = (x: number) => x * x;

const calculate = compose(square, double, addOne);
calculate(3);  // square(double(addOne(3))) = square(double(4)) = square(8) = 64

const calculate2 = pipe(addOne, double, square);
calculate2(3);  // square(double(addOne(3))) = square(double(4)) = square(8) = 64
```

**5. SQL Query Builder:**

```typescript
class QueryBuilder {
  private query: string = "";
  
  select(...columns: string[]): this {
    this.query = `SELECT ${columns.join(", ")}`;
    return this;
  }
  
  from(...tables: string[]): this {
    this.query += ` FROM ${tables.join(", ")}`;
    return this;
  }
  
  where(...conditions: string[]): this {
    this.query += ` WHERE ${conditions.join(" AND ")}`;
    return this;
  }
  
  orderBy(...columns: string[]): this {
    this.query += ` ORDER BY ${columns.join(", ")}`;
    return this;
  }
  
  build(): string {
    return this.query;
  }
}

// Usage
const query = new QueryBuilder()
  .select("id", "name", "email")
  .from("users")
  .where("active = 1", "age > 18")
  .orderBy("name", "created_at DESC")
  .build();

// "SELECT id, name, email FROM users WHERE active = 1 AND age > 18 ORDER BY name, created_at DESC"
```

---

#### Rest Parameters vs Arguments Object

**Differences:**

```typescript
// ❌ OLD WAY: arguments object (not type-safe)
function oldSum() {
  // arguments is not typed
  let total = 0;
  for (let i = 0; i < arguments.length; i++) {
    total += arguments[i];  // No type checking
  }
  return total;
}

// ✅ NEW WAY: Rest parameters (type-safe)
function newSum(...numbers: number[]): number {
  return numbers.reduce((sum, num) => sum + num, 0);
}

// Rest parameters are real arrays
function demonstrate(...items: string[]): void {
  // All array methods available
  items.forEach(item => console.log(item));
  items.map(item => item.toUpperCase());
  items.filter(item => item.length > 3);
  
  // Type-safe
  // items[0].someMethod();  // Error if method doesn't exist
}
```

**Benefits of Rest Parameters:**
- ✅ Full type safety
- ✅ Array methods available
- ✅ Works with arrow functions
- ✅ Better IDE support
- ✅ Clearer intent
- ✅ Modern JavaScript standard

---

#### Rest Parameters with Spread Operator

**1. Forwarding Arguments:**

```typescript
// Forward arguments to another function
function logAndSum(...numbers: number[]): number {
  console.log("Numbers:", numbers);
  return sum(...numbers);  // Spread to pass as individual arguments
}

function sum(...numbers: number[]): number {
  return numbers.reduce((a, b) => a + b, 0);
}

logAndSum(1, 2, 3, 4);
```

**2. Combining with Spread:**

```typescript
// Merge arrays using rest and spread
function mergeArrays<T>(...arrays: T[][]): T[] {
  return [].concat(...arrays);
  // Or: return arrays.flat();
}

mergeArrays([1, 2], [3, 4], [5, 6]);  // [1, 2, 3, 4, 5, 6]

// Clone and add elements
function addElements<T>(array: T[], ...newElements: T[]): T[] {
  return [...array, ...newElements];
}

const original = [1, 2, 3];
const updated = addElements(original, 4, 5, 6);
// original: [1, 2, 3] (unchanged)
// updated: [1, 2, 3, 4, 5, 6]
```

---

#### Best Practices

**1. Use Descriptive Names:**

```typescript
// ❌ BAD: Generic name
function process(...args: any[]): void {
  // What are args?
}

// ✅ GOOD: Descriptive name
function sendEmails(...recipients: string[]): void {
  recipients.forEach(email => {
    // Send email
  });
}
```

**2. Prefer Specific Types:**

```typescript
// ❌ BAD: Too permissive
function calculate(...values: any[]): number {
  return values.reduce((a, b) => a + b, 0);
}

// ✅ GOOD: Specific type
function calculate(...values: number[]): number {
  return values.reduce((a, b) => a + b, 0);
}
```

**3. Consider Optional Parameters First:**

```typescript
// If you have a small, known number of parameters
function greet(name: string, title?: string, suffix?: string): string {
  let result = name;
  if (title) result = `${title} ${result}`;
  if (suffix) result = `${result}, ${suffix}`;
  return result;
}

// Use rest parameters for truly variable numbers
function concatenate(...strings: string[]): string {
  return strings.join(" ");
}
```

**4. Document Expected Arguments:**

```typescript
/**
 * Calculates the average of provided numbers
 * @param numbers - Variable number of numeric values
 * @returns The arithmetic mean
 * @throws Error if no numbers provided
 */
function average(...numbers: number[]): number {
  if (numbers.length === 0) {
    throw new Error("At least one number required");
  }
  return sum(...numbers) / numbers.length;
}
```

**5. Validate Rest Parameters:**

```typescript
function createUser(name: string, ...roles: string[]): object {
  // Validate minimum requirements
  if (roles.length === 0) {
    throw new Error("At least one role is required");
  }
  
  // Validate role values
  const validRoles = ["admin", "user", "moderator"];
  const invalidRoles = roles.filter(role => !validRoles.includes(role));
  
  if (invalidRoles.length > 0) {
    throw new Error(`Invalid roles: ${invalidRoles.join(", ")}`);
  }
  
  return { name, roles };
}
```

---

#### Common Pitfalls

**1. Forgetting Rest Parameter is an Array:**

```typescript
// ❌ WRONG: Treating as single value
function sum(...numbers: number[]): number {
  return numbers + 10;  // Error: Can't add array to number
}

// ✅ CORRECT: Iterate or reduce
function sum(...numbers: number[]): number {
  return numbers.reduce((a, b) => a + b, 0) + 10;
}
```

**2. Rest Parameter Position:**

```typescript
// ❌ WRONG: Rest parameter not last
// function invalid(...items: string[], flag: boolean): void {}

// ✅ CORRECT: Rest parameter is last
function valid(flag: boolean, ...items: string[]): void {
  console.log({ flag, items });
}
```

**3. Confusing Rest and Spread:**

```typescript
// Rest: Collects arguments into array
function restExample(...numbers: number[]): void {
  console.log(numbers);  // Array
}

// Spread: Expands array into individual arguments
const arr = [1, 2, 3];
restExample(...arr);  // Spread arr, then collect with rest

// Both in one function
function combined(...numbers: number[]): number {
  return Math.max(...numbers);  // Rest to collect, spread to pass
}
```

---

#### Summary

**Rest Parameters Syntax:**

```typescript
// Basic syntax
function func(...params: Type[]): ReturnType {
  // params is an array
}

// With other parameters (rest must be last)
function func(param1: Type1, param2: Type2, ...rest: Type3[]): ReturnType {
  // rest collects remaining arguments
}
```

**Key Points:**
- 🎯 Collect variable number of arguments into array
- 📦 Must be last parameter
- ✅ Full type safety
- 🔧 Real array with all array methods
- 💡 Better than `arguments` object
- 🎨 Works with arrow functions
- 🔄 Often used with spread operator
- 📝 Use descriptive parameter names
- ⚠️ Only one rest parameter allowed

**Common Use Cases:**
- Math operations (sum, average, min, max)
- Logging and debugging
- Event handling
- Function composition
- Query builders
- Validation chains
- Collection utilities
- API wrappers

**Quick Reference:**

```typescript
// Simple rest
(...nums: number[]) => nums.reduce((a, b) => a + b, 0)

// With regular param
(greeting: string, ...names: string[]) => `${greeting}, ${names.join(", ")}`

// Generic rest
<T>(...items: T[]): T[] => items.filter(x => x !== null)

// In classes
class Logger {
  log(...messages: any[]): void { }
}

// Type-safe
(...values: (string | number)[]) => values.filter(v => typeof v === "string")
```

---

### 6. What are Generics in TypeScript?
Generics provide a way to create reusable components that work with multiple types while maintaining type safety.

```typescript
function identity<T>(arg: T): T {
    return arg;
}

let output = identity<string>("Hello");
```

### 6.7. What are Enums in TypeScript?

**Answer:**

Enums (short for Enumerations) allow you to define a set of **named constants**. They make code more readable and maintainable by giving meaningful names to numeric or string values.

---

#### Why Use Enums?

**Benefits:**
- ✅ More readable code (names instead of magic numbers/strings)
- ✅ Type safety
- ✅ Autocomplete support
- ✅ Prevents invalid values
- ✅ Self-documenting code

**Before Enums:**

```typescript
// ❌ Magic numbers - unclear meaning
function handleStatus(status: number) {
  if (status === 0) return "Pending";
  if (status === 1) return "Active";
  if (status === 2) return "Completed";
}

handleStatus(0); // What does 0 mean?
```

**With Enums:**

```typescript
// ✅ Clear and self-documenting
enum Status {
  Pending = 0,
  Active = 1,
  Completed = 2
}

function handleStatus(status: Status) {
  if (status === Status.Pending) return "Pending";
  if (status === Status.Active) return "Active";
  if (status === Status.Completed) return "Completed";
}

handleStatus(Status.Active); // Clear intent!
```

---

#### Numeric Enums

**1. Auto-Incrementing (Default):**

```typescript
// Default: starts at 0 and increments
enum Direction {
  Up,     // 0
  Down,   // 1
  Left,   // 2
  Right   // 3
}

let dir: Direction = Direction.Up;
console.log(dir); // 0
console.log(Direction.Down); // 1

// Use in conditionals
if (dir === Direction.Up) {
  console.log("Moving up!");
}
```

**2. Custom Starting Value:**

```typescript
// Start from 1
enum Priority {
  Low = 1,
  Medium, // 2
  High    // 3
}

console.log(Priority.Low);    // 1
console.log(Priority.Medium); // 2
console.log(Priority.High);   // 3
```

**3. Custom Values:**

```typescript
// Assign specific values
enum HttpStatus {
  OK = 200,
  Created = 201,
  BadRequest = 400,
  Unauthorized = 401,
  NotFound = 404,
  ServerError = 500
}

console.log(HttpStatus.OK);       // 200
console.log(HttpStatus.NotFound); // 404

// Use in functions
function handleResponse(status: HttpStatus) {
  switch (status) {
    case HttpStatus.OK:
      return "Success";
    case HttpStatus.NotFound:
      return "Not found";
    case HttpStatus.ServerError:
      return "Server error";
  }
}
```

**4. Computed Members:**

```typescript
// Computed enum values
enum FileAccess {
  None = 0,
  Read = 1 << 0,     // 1 (bit shift)
  Write = 1 << 1,    // 2
  Execute = 1 << 2,  // 4
  ReadWrite = Read | Write,           // 3
  ReadExecute = Read | Execute,       // 5
  WriteExecute = Write | Execute,     // 6
  All = Read | Write | Execute        // 7
}

// Check permissions with bitwise operations
function hasPermission(access: FileAccess, permission: FileAccess): boolean {
  return (access & permission) === permission;
}

const userAccess = FileAccess.ReadWrite;
console.log(hasPermission(userAccess, FileAccess.Read));  // true
console.log(hasPermission(userAccess, FileAccess.Execute)); // false
```

**5. Reverse Mapping:**

```typescript
// Numeric enums support reverse mapping
enum Role {
  Admin = 1,
  User = 2,
  Guest = 3
}

// Forward mapping: name to value
console.log(Role.Admin); // 1

// Reverse mapping: value to name
console.log(Role[1]); // "Admin"
console.log(Role[2]); // "User"

// Iterate over enum
for (let key in Role) {
  if (isNaN(Number(key))) {
    console.log(key, Role[key as keyof typeof Role]);
  }
}
// Output:
// Admin 1
// User 2
// Guest 3
```

---

#### String Enums

**1. String Enum Basics:**

```typescript
// String enums (recommended over numeric)
enum Direction {
  Up = "UP",
  Down = "DOWN",
  Left = "LEFT",
  Right = "RIGHT"
}

let dir: Direction = Direction.Up;
console.log(dir); // "UP"

// More meaningful when debugging/logging
console.log(`Moving ${Direction.Up}`); // "Moving UP"
```

**2. Why String Enums?**

```typescript
// ✅ String enums are more readable
enum LogLevel {
  Debug = "DEBUG",
  Info = "INFO",
  Warning = "WARNING",
  Error = "ERROR"
}

function log(level: LogLevel, message: string) {
  console.log(`[${level}] ${message}`);
}

log(LogLevel.Error, "Something went wrong");
// Output: [ERROR] Something went wrong

// In JSON/API responses, strings are clearer
const config = {
  logLevel: LogLevel.Info // "INFO" in JSON
};
```

**3. No Reverse Mapping:**

```typescript
enum Color {
  Red = "RED",
  Green = "GREEN",
  Blue = "BLUE"
}

console.log(Color.Red); // "RED"
// console.log(Color["RED"]); // ❌ Error: no reverse mapping
```

**4. String Enum Patterns:**

```typescript
// URL paths
enum Routes {
  Home = "/",
  About = "/about",
  Contact = "/contact",
  Profile = "/profile/:id"
}

// API endpoints
enum Endpoints {
  Users = "/api/users",
  Posts = "/api/posts",
  Comments = "/api/comments"
}

// Event names
enum Events {
  Click = "click",
  MouseMove = "mousemove",
  KeyPress = "keypress",
  Submit = "submit"
}

document.addEventListener(Events.Click, handler);

// CSS classes
enum CSSClasses {
  Active = "active",
  Hidden = "hidden",
  Disabled = "disabled"
}

element.classList.add(CSSClasses.Active);
```

---

#### Heterogeneous Enums

**Mixed String and Number (Not Recommended):**

```typescript
// Can mix types, but generally avoided
enum BooleanLikeHetero {
  No = 0,
  Yes = "YES"
}

console.log(BooleanLikeHetero.No);  // 0
console.log(BooleanLikeHetero.Yes); // "YES"

// ⚠️ Confusing - stick to one type
```

---

#### Const Enums

**1. Const Enum Basics:**

```typescript
// Const enums are completely removed at compile time
const enum Direction {
  Up,
  Down,
  Left,
  Right
}

let dir = Direction.Up;

// Compiles to:
// let dir = 0; /* Direction.Up */

// More efficient - no runtime object created
```

**2. Const vs Regular Enum:**

```typescript
// Regular enum
enum RegularColor {
  Red,
  Green,
  Blue
}

// Compiles to:
// var RegularColor;
// (function (RegularColor) {
//   RegularColor[RegularColor["Red"] = 0] = "Red";
//   RegularColor[RegularColor["Green"] = 1] = "Green";
//   RegularColor[RegularColor["Blue"] = 2] = "Blue";
// })(RegularColor || (RegularColor = {}));

// Const enum
const enum ConstColor {
  Red,
  Green,
  Blue
}

let color = ConstColor.Red;

// Compiles to just:
// let color = 0; /* ConstColor.Red */

// Much smaller bundle size!
```

**3. When to Use Const Enums:**

```typescript
// ✅ Use const enums for:
// - Internal/private enums
// - Performance-critical code
// - When you don't need reverse mapping

const enum Status {
  Pending,
  Active,
  Completed
}

function processOrder(status: Status) {
  if (status === Status.Active) {
    // Process
  }
}

// ❌ Don't use const enums for:
// - Public APIs (libraries)
// - When you need runtime enum object
// - When using --isolatedModules
```

**4. Const Enum Limitations:**

```typescript
const enum Numbers {
  One = 1,
  Two = 2
}

// ❌ Cannot access enum object at runtime
// console.log(Numbers); // Error

// ❌ Cannot iterate
// for (let num in Numbers) { } // Error

// ❌ No reverse mapping
// console.log(Numbers[1]); // Error

// ✅ Can only use member access
let num = Numbers.One; // OK
```

---

#### Ambient Enums

**Declare Enum (for external enums):**

```typescript
// Declare an enum defined elsewhere
declare enum ExternalEnum {
  A = 1,
  B,
  C
}

// Used for typing external code
let value: ExternalEnum = ExternalEnum.A;

// No code generated - assumes enum exists at runtime
```

---

#### Enum Member Types

**1. Constant Members:**

```typescript
enum E1 {
  A = 1,        // Constant
  B,            // Constant (computed from previous)
  C = 2 + 3,    // Constant (compile-time calculation)
}

// Constant members are known at compile time
```

**2. Computed Members:**

```typescript
enum E2 {
  A = getValue(),  // Computed (function call)
  B = "hello".length, // Computed
}

function getValue() {
  return 1;
}

// Computed members evaluated at runtime
```

---

#### Enum as Type

**1. Enum Member as Type:**

```typescript
enum Status {
  Pending,
  Active,
  Completed
}

// Use specific enum member as type
function handleActive(status: Status.Active) {
  // Only accepts Status.Active
}

handleActive(Status.Active);     // ✅ OK
// handleActive(Status.Pending); // ❌ Error
```

**2. Enum as Union Type:**

```typescript
enum Direction {
  Up = "UP",
  Down = "DOWN",
  Left = "LEFT",
  Right = "RIGHT"
}

// Enum type is union of all members
type Dir = Direction; // "UP" | "DOWN" | "LEFT" | "RIGHT"

function move(direction: Direction) {
  // direction must be one of the enum values
}

move(Direction.Up); // ✅ OK
// move("UP");      // ❌ Error (string, not enum)
```

---

#### Enums vs Union Types

**When to Use Enums:**

```typescript
// ✅ Use enums for:
// 1. Related constants that form a group
enum HttpMethod {
  GET = "GET",
  POST = "POST",
  PUT = "PUT",
  DELETE = "DELETE"
}

// 2. When you need runtime object
function getAllMethods() {
  return Object.values(HttpMethod);
}

// 3. Bitwise flags
enum Permissions {
  Read = 1,
  Write = 2,
  Execute = 4
}
```

**When to Use Union Types:**

```typescript
// ✅ Use unions for:
// 1. Simple string literals
type Status = "pending" | "active" | "completed";

// 2. When you don't need runtime object
type Size = "small" | "medium" | "large";

// 3. More flexible (can include complex types)
type Result = string | number | null;

// 4. Better type narrowing
function process(status: "success" | "error") {
  if (status === "success") {
    // Narrow type
  }
}
```

**Comparison:**

```typescript
// Enum approach
enum Color {
  Red = "red",
  Green = "green",
  Blue = "blue"
}

let color: Color = Color.Red;
color = Color.Green; // ✅ OK
// color = "red";    // ❌ Error

// Union type approach
type ColorType = "red" | "green" | "blue";

let colorType: ColorType = "red";
colorType = "green"; // ✅ OK
colorType = "red";   // ✅ OK (direct string assignment)

// Enums: Type safety + Runtime object
// Unions: Type safety only (no runtime overhead)
```

---

#### Real-World Enum Examples

**1. Application State:**

```typescript
enum AppState {
  Idle = "IDLE",
  Loading = "LOADING",
  Success = "SUCCESS",
  Error = "ERROR"
}

class Application {
  private state: AppState = AppState.Idle;
  
  setState(newState: AppState) {
    console.log(`Transitioning from ${this.state} to ${newState}`);
    this.state = newState;
  }
  
  async loadData() {
    this.setState(AppState.Loading);
    try {
      const data = await fetch("/api/data");
      this.setState(AppState.Success);
    } catch (error) {
      this.setState(AppState.Error);
    }
  }
}
```

**2. User Roles:**

```typescript
enum UserRole {
  Admin = "ADMIN",
  Moderator = "MODERATOR",
  User = "USER",
  Guest = "GUEST"
}

interface User {
  id: number;
  name: string;
  role: UserRole;
}

function hasPermission(user: User, requiredRole: UserRole): boolean {
  const hierarchy = {
    [UserRole.Guest]: 0,
    [UserRole.User]: 1,
    [UserRole.Moderator]: 2,
    [UserRole.Admin]: 3
  };
  
  return hierarchy[user.role] >= hierarchy[requiredRole];
}

const user: User = { id: 1, name: "Alice", role: UserRole.Admin };
console.log(hasPermission(user, UserRole.Moderator)); // true
```

**3. Game States:**

```typescript
enum GameState {
  Menu = "MENU",
  Playing = "PLAYING",
  Paused = "PAUSED",
  GameOver = "GAME_OVER"
}

class Game {
  private state: GameState = GameState.Menu;
  
  start() {
    if (this.state === GameState.Menu) {
      this.state = GameState.Playing;
    }
  }
  
  pause() {
    if (this.state === GameState.Playing) {
      this.state = GameState.Paused;
    }
  }
  
  resume() {
    if (this.state === GameState.Paused) {
      this.state = GameState.Playing;
    }
  }
  
  end() {
    this.state = GameState.GameOver;
  }
}
```

**4. HTTP Status Codes:**

```typescript
const enum HttpStatus {
  // 2xx Success
  OK = 200,
  Created = 201,
  Accepted = 202,
  NoContent = 204,
  
  // 3xx Redirection
  MovedPermanently = 301,
  Found = 302,
  NotModified = 304,
  
  // 4xx Client Errors
  BadRequest = 400,
  Unauthorized = 401,
  Forbidden = 403,
  NotFound = 404,
  MethodNotAllowed = 405,
  
  // 5xx Server Errors
  InternalServerError = 500,
  NotImplemented = 501,
  BadGateway = 502,
  ServiceUnavailable = 503
}

class ApiResponse {
  constructor(
    public status: HttpStatus,
    public data?: any,
    public error?: string
  ) {}
  
  isSuccess(): boolean {
    return this.status >= 200 && this.status < 300;
  }
  
  isClientError(): boolean {
    return this.status >= 400 && this.status < 500;
  }
  
  isServerError(): boolean {
    return this.status >= 500 && this.status < 600;
  }
}

// Usage
const response = new ApiResponse(HttpStatus.OK, { user: "Alice" });
if (response.isSuccess()) {
  console.log(response.data);
}
```

**5. Sorting and Ordering:**

```typescript
enum SortOrder {
  Ascending = "ASC",
  Descending = "DESC"
}

enum SortField {
  Name = "name",
  Date = "date",
  Price = "price"
}

interface SortOptions {
  field: SortField;
  order: SortOrder;
}

function sortData<T>(data: T[], options: SortOptions): T[] {
  return data.sort((a, b) => {
    const aValue = a[options.field];
    const bValue = b[options.field];
    
    if (options.order === SortOrder.Ascending) {
      return aValue > bValue ? 1 : -1;
    } else {
      return aValue < bValue ? 1 : -1;
    }
  });
}
```

---

#### Enum Best Practices

**1. Prefer String Enums:**

```typescript
// ✅ GOOD: String enums are more explicit
enum Status {
  Pending = "PENDING",
  Active = "ACTIVE",
  Completed = "COMPLETED"
}

// ❌ AVOID: Numeric enums in logs are unclear
enum StatusBad {
  Pending,   // 0
  Active,    // 1
  Completed  // 2
}

// In logs/debugging:
console.log(Status.Active);    // "ACTIVE" (clear)
console.log(StatusBad.Active); // 1 (unclear)
```

**2. Use Const Enums for Performance:**

```typescript
// ✅ GOOD: Const enum for internal use
const enum InternalStatus {
  Idle,
  Processing,
  Done
}

// ✅ GOOD: Regular enum for public API
export enum PublicStatus {
  Idle = "IDLE",
  Processing = "PROCESSING",
  Done = "DONE"
}
```

**3. Be Explicit with Values:**

```typescript
// ✅ GOOD: Explicit values
enum Priority {
  Low = 1,
  Medium = 5,
  High = 10
}

// ❌ AVOID: Relying on auto-increment
enum PriorityBad {
  Low,    // 0
  Medium, // 1
  High    // 2
}
// What if you add VeryLow = 0 later?
```

**4. Group Related Constants:**

```typescript
// ✅ GOOD: Related constants in one enum
enum FileType {
  Image = "IMAGE",
  Video = "VIDEO",
  Audio = "AUDIO",
  Document = "DOCUMENT"
}

// ❌ AVOID: Multiple separate constants
const IMAGE = "IMAGE";
const VIDEO = "VIDEO";
const AUDIO = "AUDIO";
```

**5. Use Enums for Fixed Sets:**

```typescript
// ✅ GOOD: Fixed set of values
enum Weekday {
  Monday,
  Tuesday,
  Wednesday,
  Thursday,
  Friday,
  Saturday,
  Sunday
}

// ❌ AVOID: Enums for open-ended values
// enum UserName { } // Names aren't fixed!
type UserName = string; // Better
```

---

#### Common Pitfalls

**1. String Coercion:**

```typescript
enum Status {
  Active = "ACTIVE",
  Inactive = "INACTIVE"
}

// ❌ Cannot assign string directly
// let status: Status = "ACTIVE"; // Error

// ✅ Must use enum member
let status: Status = Status.Active;

// Workaround for external data
function parseStatus(str: string): Status | undefined {
  return Object.values(Status).includes(str as Status) 
    ? str as Status 
    : undefined;
}
```

**2. Enum Merging:**

```typescript
enum Color {
  Red,
  Green,
  Blue
}

// Can add more members (declaration merging)
enum Color {
  Yellow,  // Continues from 3
  Orange   // 4
}

// ⚠️ Be careful with merging - can be confusing
```

**3. Const Enum in Libraries:**

```typescript
// ❌ DON'T export const enums from libraries
export const enum LibraryEnum {
  A, B, C
}

// Consumers can't access the enum object
// Breaking change if you switch to regular enum

// ✅ DO use regular enums for public APIs
export enum LibraryEnum {
  A = "A",
  B = "B",
  C = "C"
}
```

---

#### Summary

**Quick Reference:**

```typescript
// Numeric enum (auto-increment from 0)
enum Direction { Up, Down, Left, Right }

// Numeric with custom start
enum Priority { Low = 1, Medium, High }

// String enum (recommended)
enum Status {
  Active = "ACTIVE",
  Inactive = "INACTIVE"
}

// Const enum (inlined at compile time)
const enum HttpStatus {
  OK = 200,
  NotFound = 404
}

// Reverse mapping (numeric only)
enum Role { Admin = 1, User = 2 }
console.log(Role[1]); // "Admin"

// Use in types
function handle(status: Status) { }

// Iterate enum
Object.keys(Status).forEach(key => {
  console.log(key, Status[key as keyof typeof Status]);
});
```

**Key Principles:**
- ✅ Use string enums for better debugging
- ✅ Use const enums for performance (internal use)
- ✅ Be explicit with values
- ✅ Group related constants
- ✅ Consider union types for simple cases
- ✅ Use enums when you need runtime object
- ✅ Export regular (not const) enums from libraries
- ⚠️ Avoid heterogeneous enums
- ⚠️ Don't use enums for open-ended values

**Enums vs Alternatives:**
- **Enums**: Type safety + runtime object + reverse mapping
- **Union types**: Type safety only (smaller bundle, more flexible)
- **Constants**: No type safety, but simple
- **Objects as const**: Combines benefits of enums and unions

---

### 6.8. What are Literal Types in TypeScript?

**Answer:**

Literal types allow you to specify **exact values** that a variable can have, rather than just the general type. They combine TypeScript's type system with specific concrete values, providing precise type constraints.

---

#### What are Literal Types?

**Definition:**
A literal type is a type that represents a specific exact value rather than a range of values.

**Comparison:**

```typescript
// Regular type - any string is allowed
let regularString: string = "hello";
regularString = "world"; // ✅ OK
regularString = "anything"; // ✅ OK

// Literal type - only specific string allowed
let literalString: "hello" = "hello";
// literalString = "world"; // ❌ Error!
// literalString = "anything"; // ❌ Error!

// Regular type - any number
let regularNumber: number = 42;

// Literal type - only specific number
let literalNumber: 42 = 42;
// literalNumber = 43; // ❌ Error!
```

---

#### String Literal Types

**1. Single String Literal:**

```typescript
// Only accepts exactly "GET"
let method: "GET" = "GET";
// method = "POST"; // ❌ Error

// Function parameter
function handleGet(method: "GET") {
  console.log(`Handling ${method} request`);
}

handleGet("GET"); // ✅ OK
// handleGet("POST"); // ❌ Error
```

**2. Union of String Literals:**

```typescript
// One of specific strings
type HttpMethod = "GET" | "POST" | "PUT" | "PATCH" | "DELETE";

function request(url: string, method: HttpMethod) {
  console.log(`${method} ${url}`);
}

request("/api/users", "GET");    // ✅ OK
request("/api/users", "POST");   // ✅ OK
// request("/api/users", "FETCH"); // ❌ Error

// Provides autocomplete in IDE!
```

**3. Direction and Position:**

```typescript
// Cardinal directions
type Direction = "north" | "south" | "east" | "west";

function move(direction: Direction, steps: number) {
  console.log(`Moving ${steps} steps ${direction}`);
}

move("north", 5); // ✅ OK
// move("up", 5);  // ❌ Error

// Alignment
type Alignment = "left" | "center" | "right" | "justify";
type VerticalAlign = "top" | "middle" | "bottom";

interface TextStyle {
  align: Alignment;
  verticalAlign: VerticalAlign;
}
```

**4. Configuration Options:**

```typescript
// Log levels
type LogLevel = "debug" | "info" | "warn" | "error" | "fatal";

class Logger {
  constructor(private level: LogLevel = "info") {}
  
  log(level: LogLevel, message: string) {
    console.log(`[${level.toUpperCase()}] ${message}`);
  }
}

const logger = new Logger("debug");
logger.log("info", "Application started");

// Environment
type Environment = "development" | "staging" | "production";

const config: { env: Environment; debug: boolean } = {
  env: "production",
  debug: false
};
```

**5. Status and State:**

```typescript
// Application state
type AppState = "idle" | "loading" | "success" | "error";

interface State {
  status: AppState;
  data?: any;
  error?: string;
}

function handleState(state: State) {
  switch (state.status) {
    case "idle":
      console.log("Waiting...");
      break;
    case "loading":
      console.log("Loading...");
      break;
    case "success":
      console.log("Data:", state.data);
      break;
    case "error":
      console.error("Error:", state.error);
      break;
  }
}

// Order status
type OrderStatus = "pending" | "processing" | "shipped" | "delivered" | "cancelled";
```

---

#### Numeric Literal Types

**1. Single Numeric Literal:**

```typescript
// Only accepts exactly 42
let answer: 42 = 42;
// answer = 43; // ❌ Error

// Use in types
type Port3000 = 3000;
let devPort: Port3000 = 3000;
```

**2. Union of Numeric Literals:**

```typescript
// Dice roll (1-6)
type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;

function rollDice(): DiceRoll {
  return (Math.floor(Math.random() * 6) + 1) as DiceRoll;
}

const roll: DiceRoll = rollDice();

// HTTP status codes
type SuccessStatus = 200 | 201 | 202 | 204;
type ClientError = 400 | 401 | 403 | 404;
type ServerError = 500 | 502 | 503 | 504;
type HttpStatus = SuccessStatus | ClientError | ServerError;

function handleStatus(status: HttpStatus) {
  if (status >= 200 && status < 300) {
    console.log("Success!");
  } else if (status >= 400 && status < 500) {
    console.error("Client error");
  } else {
    console.error("Server error");
  }
}

handleStatus(200); // ✅ OK
handleStatus(404); // ✅ OK
// handleStatus(999); // ❌ Error
```

**3. Version Numbers:**

```typescript
// API versions
type ApiVersion = 1 | 2 | 3;

function callApi(version: ApiVersion, endpoint: string) {
  return fetch(`/api/v${version}/${endpoint}`);
}

callApi(2, "users"); // ✅ OK
// callApi(4, "users"); // ❌ Error

// Pagination
type PageSize = 10 | 25 | 50 | 100;

interface PaginationOptions {
  page: number;
  pageSize: PageSize;
}
```

**4. Ratings and Scores:**

```typescript
// Star rating (1-5)
type StarRating = 1 | 2 | 3 | 4 | 5;

interface Review {
  rating: StarRating;
  comment: string;
}

const review: Review = {
  rating: 5,
  comment: "Excellent!"
};

// Priority levels
type Priority = 0 | 1 | 2 | 3;

interface Task {
  title: string;
  priority: Priority; // 0 = low, 3 = critical
}
```

---

#### Boolean Literal Types

**1. true and false Literals:**

```typescript
// Always true
type AlwaysTrue = true;
let isTrue: AlwaysTrue = true;
// isTrue = false; // ❌ Error

// Always false
type AlwaysFalse = false;
let isFalse: AlwaysFalse = false;
// isFalse = true; // ❌ Error
```

**2. Discriminated Unions (Most Common Use):**

```typescript
// Result type with boolean discriminant
type Result<T> =
  | { success: true; data: T }
  | { success: false; error: string };

function parseJSON<T>(json: string): Result<T> {
  try {
    const data = JSON.parse(json);
    return { success: true, data };
  } catch (error) {
    return { success: false, error: error.message };
  }
}

// TypeScript narrows the type based on success
const result = parseJSON<{ name: string }>('{"name":"Alice"}');

if (result.success) {
  // TypeScript knows: result = { success: true; data: T }
  console.log(result.data.name);
} else {
  // TypeScript knows: result = { success: false; error: string }
  console.error(result.error);
}

// API Response
type ApiResponse<T> =
  | { ok: true; status: 200; body: T }
  | { ok: false; status: number; error: string };
```

**3. Configuration Flags:**

```typescript
// Feature flags with literal types
interface Features {
  darkMode: true | false;
  betaFeatures: true | false;
  analytics: true | false;
}

const features: Features = {
  darkMode: true,
  betaFeatures: false,
  analytics: true
};

// Though usually just boolean is clearer:
interface BetterFeatures {
  darkMode: boolean;
  betaFeatures: boolean;
  analytics: boolean;
}
```

---

#### Template Literal Types

**1. Basic Template Literals (TypeScript 4.1+):**

```typescript
// Combine string literals
type Color = "red" | "blue" | "green";
type Size = "small" | "medium" | "large";

// Creates: "red-small" | "red-medium" | ... (9 combinations)
type ColoredSize = `${Color}-${Size}`;

let style: ColoredSize = "red-small"; // ✅ OK
// let invalid: ColoredSize = "red-tiny"; // ❌ Error

// Use in objects
interface Product {
  id: string;
  style: ColoredSize;
}
```

**2. Event Names:**

```typescript
// Generate event names
type Events = "click" | "focus" | "blur";
type EventHandlers = `on${Capitalize<Events>}`;
// "onClick" | "onFocus" | "onBlur"

interface Component {
  onClick?: () => void;
  onFocus?: () => void;
  onBlur?: () => void;
}

// Property change events
type PropEventSource<T> = {
  on<K extends string & keyof T>(
    eventName: `${K}Changed`,
    callback: (newValue: T[K]) => void
  ): void;
};

declare function makeWatchedObject<T>(obj: T): T & PropEventSource<T>;

const person = makeWatchedObject({
  firstName: "Alice",
  age: 30
});

person.on("firstNameChanged", newName => {
  // newName is inferred as string
  console.log(`New name: ${newName}`);
});

person.on("ageChanged", newAge => {
  // newAge is inferred as number
  console.log(`New age: ${newAge}`);
});
```

**3. CSS Units:**

```typescript
// CSS measurements
type CSSUnit = "px" | "em" | "rem" | "%" | "vh" | "vw";
type CSSValue = `${number}${CSSUnit}`;

let width: CSSValue = "100px";
let height: CSSValue = "50%";
let fontSize: CSSValue = "1.5rem";
// let invalid: CSSValue = "100"; // ❌ Error (no unit)

// Color hex codes
type HexDigit = "0" | "1" | "2" | "3" | "4" | "5" | "6" | "7" | "8" | "9" | "a" | "b" | "c" | "d" | "e" | "f";
type HexColor = `#${HexDigit}${HexDigit}${HexDigit}${HexDigit}${HexDigit}${HexDigit}`;
// Too verbose - usually just: type HexColor = `#${string}`;
```

**4. URL Patterns:**

```typescript
// HTTP/HTTPS URLs
type Protocol = "http" | "https";
type URL = `${Protocol}://${string}`;

let apiUrl: URL = "https://api.example.com";
// let invalid: URL = "ftp://example.com"; // ❌ Error

// Route paths
type Method = "GET" | "POST" | "PUT" | "DELETE";
type Route = `/${string}`;
type Endpoint = `${Method} ${Route}`;

type Endpoints = 
  | "GET /users"
  | "POST /users"
  | "GET /users/:id"
  | "PUT /users/:id"
  | "DELETE /users/:id";
```

**5. Intrinsic String Manipulation:**

```typescript
// Built-in string transformations
type Greeting = "hello world";

type LoudGreeting = Uppercase<Greeting>; // "HELLO WORLD"
type QuietGreeting = Lowercase<Greeting>; // "hello world"
type CapitalizedGreeting = Capitalize<Greeting>; // "Hello world"
type UncapitalizedGreeting = Uncapitalize<Greeting>; // "hello world"

// Real-world example
type HttpMethod = "get" | "post" | "put" | "delete";
type MethodName = `do${Capitalize<HttpMethod>}`;
// "doGet" | "doPost" | "doPut" | "doDelete"

class ApiClient {
  doGet() { /* ... */ }
  doPost() { /* ... */ }
  doPut() { /* ... */ }
  doDelete() { /* ... */ }
}
```

---

#### Mixed Literal Types

**1. Combining Different Literals:**

```typescript
// Mix strings, numbers, and booleans
type Mixed = "text" | 42 | true;

let value: Mixed;
value = "text"; // ✅ OK
value = 42;     // ✅ OK
value = true;   // ✅ OK
// value = "other"; // ❌ Error
// value = 43;      // ❌ Error
// value = false;   // ❌ Error

// Practical example
type Status = "pending" | "active" | 0 | 1;
// Though mixing types is usually confusing
```

**2. Null and Undefined Literals:**

```typescript
// Include null/undefined as literal
type NullableString = string | null;
type OptionalNumber = number | undefined;
type MaybeValue = string | null | undefined;

// In discriminated unions
type LoadingState<T> =
  | { status: "loading"; data: undefined }
  | { status: "success"; data: T }
  | { status: "error"; data: undefined; error: string };
```

---

#### Const Assertions

**1. As Const for Literals:**

```typescript
// Without as const
let x = "hello"; // type: string
let y = 42;      // type: number

// With as const
let a = "hello" as const; // type: "hello"
let b = 42 as const;      // type: 42

// In arrays
let arr1 = [1, 2, 3];           // type: number[]
let arr2 = [1, 2, 3] as const;  // type: readonly [1, 2, 3]

// Can't modify
// arr2[0] = 10; // ❌ Error: readonly
// arr2.push(4); // ❌ Error: readonly
```

**2. Object Literals:**

```typescript
// Without as const
const config1 = {
  host: "localhost",
  port: 3000
};
// Type: { host: string; port: number }

// With as const
const config2 = {
  host: "localhost",
  port: 3000
} as const;
// Type: { readonly host: "localhost"; readonly port: 3000 }

// Deeply readonly
const routes = {
  api: {
    users: "/api/users",
    posts: "/api/posts"
  }
} as const;

// routes.api.users = "/new"; // ❌ Error: readonly
```

**3. Enum-like Objects:**

```typescript
// Create enum-like object with as const
const Direction = {
  Up: "UP",
  Down: "DOWN",
  Left: "LEFT",
  Right: "RIGHT"
} as const;

// Extract type from values
type Direction = typeof Direction[keyof typeof Direction];
// "UP" | "DOWN" | "LEFT" | "RIGHT"

function move(direction: Direction) {
  console.log(`Moving ${direction}`);
}

move(Direction.Up);   // ✅ OK
move("UP");           // ✅ OK
// move("FORWARD");   // ❌ Error

// Benefits over enum:
// - No transpiled code
// - Can use string directly
// - More flexible
```

**4. Tuple with as const:**

```typescript
// Without as const
const point1 = [10, 20];
// Type: number[]
point1.push(30); // ✅ OK (mutable array)

// With as const
const point2 = [10, 20] as const;
// Type: readonly [10, 20]
// point2.push(30); // ❌ Error
// point2[0] = 5;   // ❌ Error

// Function return with as const
function getCoordinates() {
  return [10, 20] as const;
}

const [x, y] = getCoordinates();
// x: 10, y: 20 (literal types, not number)
```

---

#### Literal Type Widening

**1. Understanding Widening:**

```typescript
// let widens to general type
let str = "hello"; // type: string (widened)
str = "world";     // ✅ OK

// const preserves literal
const constStr = "hello"; // type: "hello" (literal)
// constStr = "world";    // ❌ Error

// Widening in objects
const obj = {
  name: "Alice" // type: string (widened)
};
obj.name = "Bob"; // ✅ OK

// Prevent widening with as const
const literal = {
  name: "Alice" // type: "Alice" (literal)
} as const;
// literal.name = "Bob"; // ❌ Error
```

**2. Controlling Widening:**

```typescript
// Function parameters widen
function log(level: "info" | "error") {
  console.log(level);
}

let level = "info"; // type: string (widened)
// log(level);      // ❌ Error: string not assignable to "info" | "error"

// Solution 1: Type annotation
let level1: "info" | "error" = "info";
log(level1); // ✅ OK

// Solution 2: as const
let level2 = "info" as const;
log(level2); // ✅ OK

// Solution 3: Satisfy (TypeScript 4.9+)
let level3 = "info" satisfies "info" | "error";
log(level3); // ✅ OK
```

**3. Contextual Typing:**

```typescript
// Context provides literal type
type Config = {
  mode: "development" | "production";
};

const config: Config = {
  mode: "development" // Literal type from context
};

// Array context
const methods: ("GET" | "POST")[] = ["GET", "POST"];

// Function parameter context
function handle(status: 200 | 404) {}
handle(200); // Literal inferred from parameter type
```

---

#### Real-World Patterns

**1. Configuration Objects:**

```typescript
// Database configuration
type DatabaseType = "postgres" | "mysql" | "mongodb";
type LogLevel = "debug" | "info" | "warn" | "error";

interface DatabaseConfig {
  type: DatabaseType;
  host: string;
  port: number;
  logLevel: LogLevel;
}

const dbConfig: DatabaseConfig = {
  type: "postgres",
  host: "localhost",
  port: 5432,
  logLevel: "info"
};

// Autocomplete suggests: "postgres" | "mysql" | "mongodb"
```

**2. Action Creators (Redux-style):**

```typescript
// Action types
type ActionType = 
  | "USER_LOGIN"
  | "USER_LOGOUT"
  | "FETCH_DATA"
  | "UPDATE_PROFILE";

interface Action<T extends ActionType> {
  type: T;
  payload?: any;
}

// Type-safe action creators
function createAction<T extends ActionType>(
  type: T
): (payload?: any) => Action<T> {
  return (payload) => ({ type, payload });
}

const login = createAction("USER_LOGIN");
const logout = createAction("USER_LOGOUT");

const action = login({ username: "alice" });
// action.type is "USER_LOGIN" (literal type)
```

**3. Type-Safe Form Fields:**

```typescript
// Form field names as literals
type FormField = "username" | "email" | "password" | "confirmPassword";

interface FormState {
  values: Record<FormField, string>;
  errors: Partial<Record<FormField, string>>;
  touched: Partial<Record<FormField, boolean>>;
}

class Form {
  private state: FormState = {
    values: {
      username: "",
      email: "",
      password: "",
      confirmPassword: ""
    },
    errors: {},
    touched: {}
  };
  
  setValue(field: FormField, value: string) {
    this.state.values[field] = value;
  }
  
  setError(field: FormField, error: string) {
    this.state.errors[field] = error;
  }
  
  // Autocomplete works for field names!
}
```

**4. API Route Definitions:**

```typescript
// Define all routes with literal types
type ApiRoute = 
  | "/api/users"
  | "/api/users/:id"
  | "/api/posts"
  | "/api/posts/:id"
  | "/api/comments";

type HttpMethod = "GET" | "POST" | "PUT" | "PATCH" | "DELETE";

interface RouteConfig {
  method: HttpMethod;
  path: ApiRoute;
  handler: (req: any, res: any) => void;
}

const routes: RouteConfig[] = [
  {
    method: "GET",
    path: "/api/users",
    handler: (req, res) => { /* ... */ }
  },
  {
    method: "POST",
    path: "/api/users",
    handler: (req, res) => { /* ... */ }
  }
  // Autocomplete suggests valid paths!
];
```

---

#### Literal Types vs Enums

**When to Use Literal Types:**

```typescript
// ✅ Use literal types for:

// 1. Simple string constants
type Status = "pending" | "active" | "inactive";

// 2. No runtime object needed
type Color = "red" | "green" | "blue";

// 3. More flexible (can include other types)
type Value = "text" | 42 | null;

// 4. Better JSON serialization
const data = { status: "active" }; // Just a string in JSON

// 5. Can use strings directly
function process(status: Status) {}
process("active"); // ✅ Direct string works
```

**When to Use Enums:**

```typescript
// ✅ Use enums for:

// 1. Need runtime object
enum Color { Red, Green, Blue }
Object.keys(Color); // Get all values at runtime

// 2. Numeric values
enum Priority { Low = 1, Medium = 5, High = 10 }

// 3. Bitwise operations
enum Permission { Read = 1, Write = 2, Execute = 4 }

// 4. Reverse mapping needed
enum Role { Admin = 1, User = 2 }
console.log(Role[1]); // "Admin"

// 5. Grouped constants
enum HttpStatus {
  OK = 200,
  NotFound = 404,
  ServerError = 500
}
```

---

#### Best Practices

**1. Use Literal Types for APIs:**

```typescript
// ✅ GOOD: API contracts with literal types
interface ApiRequest {
  method: "GET" | "POST" | "PUT" | "DELETE";
  endpoint: string;
  body?: Record<string, any>;
}

// ❌ BAD: Too permissive
interface BadRequest {
  method: string; // Any string!
  endpoint: string;
}
```

**2. Combine with Union Types:**

```typescript
// ✅ GOOD: Precise types
type Result<T> = 
  | { status: "success"; data: T }
  | { status: "error"; error: string }
  | { status: "loading" };

// ❌ BAD: Loose types
type BadResult<T> = {
  status: string;
  data?: T;
  error?: string;
};
```

**3. Use as const for Constants:**

```typescript
// ✅ GOOD: Preserve literals
const ROLES = ["admin", "user", "guest"] as const;
type Role = typeof ROLES[number]; // "admin" | "user" | "guest"

// ❌ BAD: Loses literal types
const BAD_ROLES = ["admin", "user", "guest"];
type BadRole = typeof BAD_ROLES[number]; // string
```

**4. Provide Clear Error Messages:**

```typescript
// ✅ GOOD: Clear constraint
type Size = "small" | "medium" | "large";

function setSize(size: Size) {
  // TypeScript error message shows valid options
}

// setSize("tiny"); 
// Error: Argument of type '"tiny"' is not assignable to parameter of type '"small" | "medium" | "large"'
```

**5. Use Template Literals for Patterns:**

```typescript
// ✅ GOOD: Pattern matching
type EventName = `on${Capitalize<string>}`;

interface Events {
  onClick: () => void;
  onSubmit: () => void;
  // TypeScript ensures all start with "on"
}
```

---

#### Summary

**Quick Reference:**

```typescript
// String literals
type Method = "GET" | "POST" | "PUT" | "DELETE";

// Numeric literals
type Port = 3000 | 8080 | 8443;

// Boolean literals (in discriminated unions)
type Result = 
  | { success: true; data: any }
  | { success: false; error: string };

// Template literals
type Route = `/${string}`;

// As const
const config = {
  host: "localhost",
  port: 3000
} as const;

// Extract type
type Config = typeof config;
```

**Key Principles:**
- ✅ Use literal types for specific allowed values
- ✅ Combine with union types for multiple options
- ✅ Use as const to preserve literal types
- ✅ Prefer literal types over enums for simple cases
- ✅ Use template literals for string patterns
- ✅ Leverage autocomplete and type narrowing
- ✅ Provide clear constraints in APIs
- ✅ Use satisfies operator to validate without widening
- ⚠️ Watch for type widening with let
- ⚠️ Be careful with contextual typing

**Literal Types Benefits:**
- 🎯 Exact value constraints
- 🔒 Type safety with specific values
- 💡 Great autocomplete support
- 📝 Self-documenting code
- 🚫 Prevents invalid values
- 🔄 Better refactoring
- 🌐 Smaller bundle (no runtime code)

---

## Intermediate Questions

### 7. Explain the concept of Type Guards
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

### 8. What are Union and Intersection types?
- **Union types** (`|`): A value can be one of several types
- **Intersection types** (`&`): Combines multiple types into one

```typescript
type StringOrNumber = string | number;
type Person = { name: string } & { age: number };
```

### 9. What is the `readonly` modifier?
The `readonly` modifier prevents properties from being changed after initialization.

```typescript
interface User {
    readonly id: number;
    name: string;
}
```

### 10. Explain `keyof` operator
`keyof` creates a union type of all property names of a given type.

```typescript
interface Person {
    name: string;
    age: number;
}

type PersonKeys = keyof Person; // "name" | "age"
```

### 11. What are Utility Types?
Built-in generic types that facilitate common type transformations:
- `Partial<T>` - Makes all properties optional
- `Required<T>` - Makes all properties required
- `Readonly<T>` - Makes all properties readonly
- `Pick<T, K>` - Picks specific properties
- `Omit<T, K>` - Omits specific properties
- `Record<K, T>` - Creates object type with specified keys and values

## Advanced Questions

### 12. What is Type Inference in TypeScript?

**Answer:**

Type inference is TypeScript's ability to **automatically deduce and assign types** without explicit type annotations. The compiler analyzes your code and determines the most appropriate type based on context, making code cleaner while maintaining type safety.

---

#### Why Type Inference Matters

**Benefits:**
- ✅ Less verbose code (fewer type annotations)
- ✅ Maintains type safety
- ✅ Reduces redundancy
- ✅ Easier refactoring
- ✅ Better developer experience

**The Balance:**

```typescript
// ❌ Too explicit (redundant)
let message: string = "Hello";
let count: number = 5;

// ✅ Good: Let TypeScript infer
let message = "Hello"; // inferred as string
let count = 5; // inferred as number

// ✅ Explicit when needed
let data: string | null = null; // Cannot be inferred
```

---

#### Variable Type Inference

**1. Basic Variable Inference:**

```typescript
// Primitive inference
let num = 42; // number
let str = "hello"; // string
let bool = true; // boolean
let big = 100n; // bigint
let sym = Symbol("key"); // symbol

// Type widening
let x = 3; // number (not literal type 3)
let y = "hello"; // string (not literal "hello")

// Const assertions preserve literals
const a = 3; // literal type 3
const b = "hello"; // literal type "hello"

// As const for objects
let config = { port: 3000 } as const;
// { readonly port: 3000 }
```

**2. Array Inference:**

```typescript
// Homogeneous arrays
let numbers = [1, 2, 3]; // number[]
let strings = ["a", "b", "c"]; // string[]

// Union type inference for mixed arrays
let mixed = [1, "two", 3]; // (string | number)[]

// Empty array needs annotation
let empty = []; // any[]
let typedEmpty: string[] = []; // string[]

// Nested arrays
let matrix = [[1, 2], [3, 4]]; // number[][]

// Best common type
let items = [1, 2, "three"]; // (string | number)[]
let values = [new Date(), "today"]; // (string | Date)[]
```

**3. Object Inference:**

```typescript
// Object literal inference
let user = {
  name: "Alice",
  age: 30,
  active: true
};
// Type: { name: string; age: number; active: boolean }

// Nested objects
let config = {
  server: {
    host: "localhost",
    port: 3000
  },
  database: {
    url: "mongodb://localhost"
  }
};

// Optional properties
let person = {
  name: "Bob",
  email: undefined // email: undefined (not string | undefined)
};

// Method inference
let calculator = {
  add(a: number, b: number) {
    return a + b; // Return type inferred as number
  }
};
```

**4. Const vs Let Inference:**

```typescript
// let: wider type
let mutableNum = 42; // number
let mutableStr = "hello"; // string

// const: narrower type (literal)
const literalNum = 42; // 42
const literalStr = "hello"; // "hello"

// Const doesn't prevent mutations of objects
const obj = { x: 10 }; // { x: number }
obj.x = 20; // ✅ OK

// Use as const for immutable objects
const immutable = { x: 10 } as const; // { readonly x: 10 }
```

---

#### Function Return Type Inference

**1. Simple Return Inference:**

```typescript
// Return type automatically inferred
function add(a: number, b: number) {
  return a + b; // Returns number
}

function greet(name: string) {
  return `Hello, ${name}`; // Returns string
}

function isEven(n: number) {
  return n % 2 === 0; // Returns boolean
}

// Arrow functions
const multiply = (a: number, b: number) => a * b; // Returns number
```

**2. Multiple Return Paths:**

```typescript
// Union type inferred from all return paths
function getValue(condition: boolean) {
  if (condition) {
    return 42; // number
  } else {
    return "default"; // string
  }
}
// Return type: string | number

// With null
function findUser(id: number) {
  if (id > 0) {
    return { id, name: "User" }; // object
  }
  return null; // null
}
// Return type: { id: number; name: string } | null
```

**3. No Return (void):**

```typescript
// Inferred as void
function logMessage(msg: string) {
  console.log(msg);
  // No return statement
}

// Explicit return undefined still infers void
function doSomething() {
  console.log("doing");
  return; // or return undefined
}
```

**4. Never Type Inference:**

```typescript
// Inferred as never (never returns)
function throwError(message: string) {
  throw new Error(message);
}

function infiniteLoop() {
  while (true) {
    // Loop forever
  }
}
```

**5. Async Function Inference:**

```typescript
// Automatically wrapped in Promise
async function fetchData() {
  return { data: "value" };
}
// Return type: Promise<{ data: string }>

async function getUser(id: number) {
  const response = await fetch(`/api/users/${id}`);
  return response.json(); // Promise<any>
}
```

---

#### Contextual Typing

**1. Callback Function Parameters:**

```typescript
// Parameter types inferred from context
const numbers = [1, 2, 3, 4, 5];

numbers.forEach(num => {
  // num is inferred as number
  console.log(num.toFixed(2));
});

numbers.map(n => n * 2); // n is number
numbers.filter(n => n > 2); // n is number

// Event handlers
button.addEventListener("click", event => {
  // event is inferred as MouseEvent
  console.log(event.clientX, event.clientY);
});
```

**2. Function Expression Context:**

```typescript
// Type from variable annotation
const greet: (name: string) => string = (name) => {
  // name is inferred as string
  return `Hello, ${name}`;
};

// Array of functions
type MathOp = (a: number, b: number) => number;

const operations: MathOp[] = [
  (a, b) => a + b, // Parameters inferred
  (a, b) => a - b,
  (a, b) => a * b,
];
```

**3. Object Method Context:**

```typescript
interface Calculator {
  add(a: number, b: number): number;
}

const calc: Calculator = {
  add(a, b) {
    // a and b are inferred as number
    return a + b;
  }
};
```

**4. Promise Context:**

```typescript
// Promise chain inference
fetch("/api/data")
  .then(response => response.json()) // response: Response
  .then(data => {
    // data: any (from json())
    console.log(data);
  });

// Explicit Promise typing
const promise: Promise<string> = new Promise((resolve, reject) => {
  // resolve and reject inferred from Promise<string>
  resolve("success"); // Must be string
});
```

---

#### Generic Type Inference

**1. Function Generics:**

```typescript
// Generic type inferred from argument
function identity<T>(arg: T): T {
  return arg;
}

const num = identity(42); // T inferred as number
const str = identity("hello"); // T inferred as string

// Multiple type parameters
function pair<T, U>(first: T, second: U): [T, U] {
  return [first, second];
}

const result = pair("age", 30); // [string, number]
```

**2. Array Generics:**

```typescript
// Generic inference with arrays
function firstElement<T>(arr: T[]): T | undefined {
  return arr[0];
}

const first = firstElement([1, 2, 3]); // number | undefined
const firstStr = firstElement(["a", "b"]); // string | undefined

// Multiple arrays
function combine<T>(arr1: T[], arr2: T[]): T[] {
  return [...arr1, ...arr2];
}

const nums = combine([1, 2], [3, 4]); // number[]
```

**3. Constraint Inference:**

```typescript
// Constrained generics
function getLength<T extends { length: number }>(obj: T): number {
  return obj.length;
}

getLength("hello"); // T inferred as string
getLength([1, 2, 3]); // T inferred as number[]
getLength({ length: 10, value: 5 }); // T inferred as { length: number; value: number }

// With return using generic
function firstChar<T extends string>(str: T): string {
  return str.charAt(0);
}

const char = firstChar("hello"); // string
```

**4. Generic Class Inference:**

```typescript
class Box<T> {
  constructor(public value: T) {}
}

const numBox = new Box(42); // Box<number>
const strBox = new Box("hello"); // Box<string>

// Explicit when needed
const anyBox = new Box<any>(someValue);
```

---

#### Best Type (Unified Type)

**1. Common Type from Array:**

```typescript
// TypeScript finds best common type
let items = [1, 2, 3]; // number[]
let mixed = [1, "two"]; // (string | number)[]

// With objects
class Animal {}
class Dog extends Animal {}
class Cat extends Animal {}

let pets = [new Dog(), new Cat()]; // (Dog | Cat)[]
let animals: Animal[] = [new Dog(), new Cat()]; // Animal[]
```

**2. Best Type from Union:**

```typescript
// Union of literals
let value = Math.random() > 0.5 ? 1 : 2; // number (widened)
let literal = Math.random() > 0.5 ? 1 as const : 2 as const; // 1 | 2

// Union from conditionals
function getValue(flag: boolean) {
  return flag ? "yes" : 42; // string | number
}
```

---

#### When to Use Explicit Types

**Explicit types are better when:**

```typescript
// 1. Function parameters (always explicit)
function greet(name: string, age: number) {
  // Clear contract
}

// 2. Public APIs
export function calculateTotal(
  items: Item[],
  taxRate: number
): { subtotal: number; tax: number; total: number } {
  // Explicit return type documents the API
}

// 3. Complex return types
function processData(): Promise<ApiResponse<UserData>> {
  // Makes intention clear
}

// 4. When inference is wrong
let data: any = externalLibrary(); // Can't infer correctly
let typed: SpecificType = externalLibrary(); // Better

// 5. Union types that can't be inferred
let status: "pending" | "active" | "inactive" = "pending";

// 6. Generic constraints
function process<T extends BaseType>(input: T): Result<T> {
  // Explicit generic makes usage clear
}
```

**Inference is better when:**

```typescript
// 1. Local variables with obvious types
let count = 0; // Obviously number
let message = "Hello"; // Obviously string

// 2. Intermediate calculations
const doubled = numbers.map(n => n * 2); // Type is clear

// 3. Object literals
const config = {
  host: "localhost",
  port: 3000
}; // Structure is obvious

// 4. Array literals
const items = [1, 2, 3]; // Obviously number[]
```

---

#### Type Inference Patterns

**1. Destructuring Inference:**

```typescript
// Array destructuring
const [first, second] = [1, 2]; // first: number, second: number

// Object destructuring
const { name, age } = { name: "Alice", age: 30 };
// name: string, age: number

// Rest parameters
const [head, ...rest] = [1, 2, 3, 4];
// head: number, rest: number[]

// Nested destructuring
const { user: { name } } = { user: { name: "Bob" } };
// name: string
```

**2. Spread Operator Inference:**

```typescript
// Array spread
const arr1 = [1, 2];
const arr2 = [3, 4];
const combined = [...arr1, ...arr2]; // number[]

// Object spread
const base = { x: 1, y: 2 };
const extended = { ...base, z: 3 };
// { x: number; y: number; z: number }

// Overriding properties
const overridden = { ...base, x: "changed" };
// { x: string; y: number }
```

**3. Discriminated Union Inference:**

```typescript
type Result =
  | { success: true; data: string }
  | { success: false; error: string };

function handleResult(result: Result) {
  if (result.success) {
    // result.data is available here
    console.log(result.data);
  } else {
    // result.error is available here
    console.error(result.error);
  }
}
```

**4. Template Literal Inference:**

```typescript
function makeUrl(protocol: "http" | "https", domain: string) {
  return `${protocol}://${domain}`;
}

const url = makeUrl("https", "example.com");
// url: string (not template literal type)

// With const assertion
const url2 = `https://example.com` as const;
// url2: "https://example.com"
```

---

#### Common Inference Pitfalls

**1. Widening:**

```typescript
// Type is widened from literal
let x = 3; // number (not 3)
let y = "hello"; // string (not "hello")

// Prevent with const
const a = 3; // 3
const b = "hello"; // "hello"

// Or as const
let c = 3 as const; // 3
```

**2. Any Leakage:**

```typescript
// any spreads through code
let data = JSON.parse('{"name":"Alice"}'); // any
let name = data.name; // any (bad!)

// Fix with type annotation
let data: { name: string } = JSON.parse('{"name":"Alice"}');
let name = data.name; // string
```

**3. Empty Array Inference:**

```typescript
// Inferred as any[]
let items = [];
items.push(1); // any[] stays any[]

// Fix with annotation
let numbers: number[] = [];
numbers.push(1); // OK
```

**4. Null/Undefined Inference:**

```typescript
// Infers as undefined, not string | undefined
let value = undefined;
value = "hello"; // ❌ Error

// Fix with union type
let value: string | undefined = undefined;
value = "hello"; // ✅ OK
```

**5. Object Excess Properties:**

```typescript
interface Config {
  host: string;
  port: number;
}

// OK with inference
const config = {
  host: "localhost",
  port: 3000,
  extra: "ignored" // OK
};

// Error with explicit type
const config2: Config = {
  host: "localhost",
  port: 3000,
  extra: "ignored" // ❌ Error
};
```

---

#### Advanced Inference Techniques

**1. Conditional Type Inference:**

```typescript
// Infer return type from function
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type Func = (x: number) => string;
type Return = ReturnType<Func>; // string
```

**2. Infer from Promise:**

```typescript
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;

type A = UnwrapPromise<Promise<string>>; // string
type B = UnwrapPromise<number>; // number
```

**3. Tuple Inference:**

```typescript
function tuple<T extends any[]>(...args: T): T {
  return args;
}

const result = tuple(1, "hello", true);
// [number, string, boolean]
```

**4. Parameter Inference:**

```typescript
type Parameters<T> = T extends (...args: infer P) => any ? P : never;

type Func = (a: string, b: number) => void;
type Params = Parameters<Func>; // [string, number]
```

---

#### Best Practices

**1. Trust Inference for Simple Cases:**

```typescript
// ✅ GOOD: Obvious types
let count = 0;
let name = "Alice";
let items = [1, 2, 3];

// ❌ BAD: Redundant annotations
let count: number = 0;
let name: string = "Alice";
let items: number[] = [1, 2, 3];
```

**2. Explicit for Function Signatures:**

```typescript
// ✅ GOOD: Explicit parameters and complex returns
function processUser(
  user: User,
  options: ProcessOptions
): ProcessResult {
  // Implementation
}

// ⚠️ AVOID: Inferring public API contracts
function processUser(user, options) {
  // Unclear contract
}
```

**3. Use const Assertions Wisely:**

```typescript
// ✅ GOOD: Preserve exact values
const config = {
  apiUrl: "https://api.example.com",
  timeout: 5000
} as const;

// Config is now deeply readonly with literal types
```

**4. Provide Hints for Generics:**

```typescript
// ✅ GOOD: Explicit when inference fails
const result = parseJSON<UserData>(jsonString);

// ✅ GOOD: Let inference work when possible
const items = Array.from([1, 2, 3]); // Infers number[]
```

**5. Avoid Any Leakage:**

```typescript
// ❌ BAD: any spreads
const data = JSON.parse(response);

// ✅ GOOD: Type the result
const data: ApiResponse = JSON.parse(response);

// ✅ BETTER: Use type-safe parser
const data = JSON.parse(response) as ApiResponse;
```

---

#### Summary

**Quick Reference:**

```typescript
// Variable inference
let num = 42; // number
const literal = 42; // 42
let arr = [1, 2, 3]; // number[]

// Function inference
function add(a: number, b: number) {
  return a + b; // Returns number
}

// Contextual typing
[1, 2, 3].map(n => n * 2); // n: number

// Generic inference
function identity<T>(x: T): T { return x; }
identity(42); // T = number

// Best type
let mixed = [1, "two"]; // (string | number)[]

// Const assertions
let config = { x: 10 } as const; // { readonly x: 10 }
```

**Key Principles:**
- ✅ Let TypeScript infer obvious types
- ✅ Be explicit for function parameters
- ✅ Be explicit for public APIs
- ✅ Use const assertions for literal types
- ✅ Trust contextual typing in callbacks
- ✅ Watch for any leakage
- ✅ Provide type hints when inference fails
- ✅ Use explicit types for complex unions
- ✅ Let return types be inferred for internal functions
- ✅ Balance explicitness with conciseness

---

### 13. How to Convert Union Types to Intersection Types in TypeScript?

**Answer:**

Converting union types to intersection types is an advanced TypeScript pattern that uses conditional types and the `infer` keyword. This technique is commonly used in utility type creation and type manipulation.

---

#### Understanding the Problem

**Union vs Intersection:**

```typescript
// Union Type: Can be ONE of the types
type Union = string | number | boolean;
let union: Union = "text";    // ✅ OK
union = 42;                   // ✅ OK
union = true;                 // ✅ OK

// Intersection Type: Must have ALL properties
type Intersection = { name: string } & { age: number } & { active: boolean };
let intersection: Intersection = {
  name: "Alice",
  age: 30,
  active: true
};
// Must have ALL properties
```

**Why Convert?**

```typescript
// Given union of object types
type A = { a: string };
type B = { b: number };
type C = { c: boolean };

type Union = A | B | C;

// Want intersection of all types
type Intersection = A & B & C;
// Result: { a: string; b: number; c: boolean }
```

---

#### The UnionToIntersection Utility Type

**Implementation:**

```typescript
/**
 * Converts a union type to an intersection type
 * Uses conditional type inference with function parameters
 */
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void 
    ? I 
    : never;

// Example usage
type Union = { a: string } | { b: number } | { c: boolean };
type Intersection = UnionToIntersection<Union>;
// Result: { a: string } & { b: number } & { c: boolean }
// = { a: string; b: number; c: boolean }
```

**How It Works:**

```typescript
// Step-by-step breakdown:

// 1. Map union to function union
type Union = { a: string } | { b: number };

// U extends any ? (k: U) => void : never
// Creates: ((k: { a: string }) => void) | ((k: { b: number }) => void)

// 2. Infer from function intersection
// TypeScript infers function parameter types contravariantly
// When inferring from a union of functions, it produces an intersection

// 3. Extract the intersection type
// Result: { a: string } & { b: number }
```

**Why Function Parameters?**

```typescript
// Function parameters are contravariant in TypeScript
// This means they flip unions to intersections

// Regular inference (covariant - preserves unions)
type GetReturnType<T> = T extends () => infer R ? R : never;
type Test1 = GetReturnType<() => string | number>;
// Result: string | number (union preserved)

// Parameter inference (contravariant - converts to intersection)
type GetParamType<T> = T extends (arg: infer P) => void ? P : never;
type Fn1 = (arg: { a: string }) => void;
type Fn2 = (arg: { b: number }) => void;
type Test2 = GetParamType<Fn1 | Fn2>;
// Result: { a: string } & { b: number } (intersection!)
```

---

#### Basic Examples

**1. Object Type Union:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// Simple objects
type User = { name: string };
type Address = { city: string };
type Contact = { email: string };

type Union = User | Address | Contact;
type Combined = UnionToIntersection<Union>;
// Result: { name: string; city: string; email: string }

const person: Combined = {
  name: "Alice",
  city: "NYC",
  email: "alice@example.com"
};
```

**2. Function Type Union:**

```typescript
type Fn1 = (a: string) => void;
type Fn2 = (b: number) => void;
type Fn3 = (c: boolean) => void;

type FnUnion = Fn1 | Fn2 | Fn3;
type FnIntersection = UnionToIntersection<FnUnion>;
// Result: ((a: string) => void) & ((b: number) => void) & ((c: boolean) => void)

// Can call with any of the parameter types
const fn: FnIntersection = (arg: any) => {
  console.log(arg);
};

fn("text");  // ✅ OK
fn(42);      // ✅ OK
fn(true);    // ✅ OK
```

**3. Primitive Types:**

```typescript
// Note: Intersection of primitives results in never
type Primitives = string | number | boolean;
type Impossible = UnionToIntersection<Primitives>;
// Result: never (can't be string AND number AND boolean)

// This only makes sense for object types
```

---

#### Advanced Use Cases

**1. Merging Multiple Interfaces:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// Merge all properties from an array of types
type MergeAll<T extends any[]> = UnionToIntersection<T[number]>;

type Types = [
  { id: number },
  { name: string },
  { age: number },
  { email: string }
];

type Merged = MergeAll<Types>;
// Result: { id: number; name: string; age: number; email: string }

const user: Merged = {
  id: 1,
  name: "Bob",
  age: 25,
  email: "bob@example.com"
};
```

**2. Extracting All Method Overloads:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// Get all overload signatures
type OverloadUnion = 
  | ((a: string) => string)
  | ((a: number) => number)
  | ((a: boolean) => boolean);

type AllOverloads = UnionToIntersection<OverloadUnion>;
// Result: Function with all overloads

const convert: AllOverloads = (a: any) => a;

const s = convert("text");   // Type: string
const n = convert(42);       // Type: number
const b = convert(true);     // Type: boolean
```

**3. Combining Plugin Interfaces:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// Plugin system
interface LoggerPlugin {
  log(message: string): void;
}

interface CachePlugin {
  cache: Map<string, any>;
  get(key: string): any;
  set(key: string, value: any): void;
}

interface MetricsPlugin {
  track(event: string): void;
}

// Union of all plugins
type PluginUnion = LoggerPlugin | CachePlugin | MetricsPlugin;

// Intersection of all plugins
type AllPlugins = UnionToIntersection<PluginUnion>;
// Result: Has ALL methods from ALL plugins

class Application implements AllPlugins {
  cache = new Map();
  
  log(message: string) {
    console.log(message);
  }
  
  get(key: string) {
    return this.cache.get(key);
  }
  
  set(key: string, value: any) {
    this.cache.set(key, value);
  }
  
  track(event: string) {
    console.log(`Tracking: ${event}`);
  }
}
```

**4. Merging Configuration Objects:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// Configuration sources
type DatabaseConfig = {
  host: string;
  port: number;
};

type CacheConfig = {
  ttl: number;
  maxSize: number;
};

type LoggingConfig = {
  level: "debug" | "info" | "error";
  file: string;
};

// Get all configs
type AllConfigs = DatabaseConfig | CacheConfig | LoggingConfig;

// Merge into single config
type MergedConfig = UnionToIntersection<AllConfigs>;

const config: MergedConfig = {
  host: "localhost",
  port: 5432,
  ttl: 3600,
  maxSize: 1000,
  level: "info",
  file: "app.log"
};
```

---

#### Combining with Other Utilities

**1. UnionToIntersection + Partial:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// Merge types with optional properties
type PartialMerge<T> = Partial<UnionToIntersection<T>>;

type Config = 
  | { database: string }
  | { cache: boolean }
  | { logging: string };

type MergedPartial = PartialMerge<Config>;
// Result: { database?: string; cache?: boolean; logging?: string }
```

**2. UnionToIntersection + Pick:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// Pick specific keys from merged type
type User = { id: number; name: string };
type Admin = { role: string; permissions: string[] };

type UserOrAdmin = User | Admin;
type Merged = UnionToIntersection<UserOrAdmin>;
// { id: number; name: string; role: string; permissions: string[] }

type Identity = Pick<Merged, "id" | "name">;
// { id: number; name: string }
```

**3. UnionToIntersection with Generics:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// Generic merge function type
type MergeObjects<T extends object[]> = UnionToIntersection<T[number]>;

function merge<T extends object[]>(...objects: T): MergeObjects<T> {
  return Object.assign({}, ...objects) as MergeObjects<T>;
}

const result = merge(
  { a: 1 },
  { b: "text" },
  { c: true }
);
// result: { a: number; b: string; c: boolean }

result.a; // ✅ number
result.b; // ✅ string
result.c; // ✅ boolean
```

---

#### Real-World Patterns

**1. Event Handler Merging:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// Event handlers from different sources
type ClickHandler = { onClick: (e: MouseEvent) => void };
type ChangeHandler = { onChange: (e: Event) => void };
type SubmitHandler = { onSubmit: (e: SubmitEvent) => void };

type AllHandlers = ClickHandler | ChangeHandler | SubmitHandler;
type MergedHandlers = UnionToIntersection<AllHandlers>;

const handlers: MergedHandlers = {
  onClick: (e) => console.log("Click"),
  onChange: (e) => console.log("Change"),
  onSubmit: (e) => console.log("Submit")
};
```

**2. API Response Type Merging:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// Different API responses
type UserData = { userId: string; username: string };
type ProfileData = { bio: string; avatar: string };
type StatsData = { posts: number; followers: number };

type ApiResponses = UserData | ProfileData | StatsData;
type CompleteProfile = UnionToIntersection<ApiResponses>;

async function fetchCompleteProfile(id: string): Promise<CompleteProfile> {
  const [user, profile, stats] = await Promise.all([
    fetch(`/api/users/${id}`).then(r => r.json()),
    fetch(`/api/profiles/${id}`).then(r => r.json()),
    fetch(`/api/stats/${id}`).then(r => r.json())
  ]);
  
  return { ...user, ...profile, ...stats };
}
```

**3. Mixin Pattern:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// Mixin types
type Timestamped = { createdAt: Date; updatedAt: Date };
type Identifiable = { id: string };
type Nameable = { name: string };

type Mixins = Timestamped | Identifiable | Nameable;
type Entity = UnionToIntersection<Mixins>;

function createEntity(): Entity {
  return {
    id: crypto.randomUUID(),
    name: "",
    createdAt: new Date(),
    updatedAt: new Date()
  };
}
```

**4. Redux Store Type Merging:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// Individual slice states
type UserState = { user: { name: string; email: string } };
type CartState = { cart: { items: any[]; total: number } };
type UIState = { ui: { loading: boolean; error: string | null } };

type SliceStates = UserState | CartState | UIState;
type RootState = UnionToIntersection<SliceStates>;

function useSelector<K extends keyof RootState>(
  selector: (state: RootState) => RootState[K]
): RootState[K] {
  // Implementation
  return {} as RootState[K];
}

// Usage
const user = useSelector(state => state.user);
const cart = useSelector(state => state.cart);
const ui = useSelector(state => state.ui);
```

---

#### Common Pitfalls

**1. Intersection of Primitives:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// ❌ BAD: Primitives become never
type Primitives = string | number | boolean;
type Impossible = UnionToIntersection<Primitives>;
// Result: never (nothing can be string AND number AND boolean)

// ✅ GOOD: Use for object types only
type Objects = { a: string } | { b: number };
type Merged = UnionToIntersection<Objects>;
// Result: { a: string; b: number }
```

**2. Conflicting Properties:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// ⚠️ WARNING: Same property, different types
type A = { value: string };
type B = { value: number };

type Conflict = UnionToIntersection<A | B>;
// Result: { value: string & number } = { value: never }

// The value property becomes never (impossible type)
```

**3. Not Understanding Contravariance:**

```typescript
// Contravariance only applies to function parameters

// ✅ CORRECT: Function parameter position
type ToIntersection1<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// ❌ WRONG: Return type position (stays union)
type StaysUnion<U> = 
  (U extends any ? () => U : never) extends 
  () => infer I ? I : never;

type Test1 = ToIntersection1<{ a: 1 } | { b: 2 }>;
// Result: { a: 1 } & { b: 2 } ✅

type Test2 = StaysUnion<{ a: 1 } | { b: 2 }>;
// Result: { a: 1 } | { b: 2 } (unchanged)
```

---

#### Best Practices

**1. Type Documentation:**

```typescript
/**
 * Converts a union type to an intersection type.
 * 
 * @example
 * type Union = { a: string } | { b: number };
 * type Intersection = UnionToIntersection<Union>;
 * // Result: { a: string; b: number }
 * 
 * @warning Only works with object types. Primitives will result in never.
 */
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;
```

**2. Guard Against Primitives:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// Add constraint for object types only
type SafeUnionToIntersection<U extends object> = UnionToIntersection<U>;

// ✅ OK
type Good = SafeUnionToIntersection<{ a: 1 } | { b: 2 }>;

// ❌ Error: string is not assignable to object
// type Bad = SafeUnionToIntersection<string | number>;
```

**3. Combine with Type Validation:**

```typescript
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// Check if result is valid (not never)
type IsNever<T> = [T] extends [never] ? true : false;

type SafeMerge<T> = 
  IsNever<UnionToIntersection<T>> extends true
    ? T  // Keep as union if intersection fails
    : UnionToIntersection<T>;  // Convert to intersection if valid

type Test1 = SafeMerge<{ a: 1 } | { b: 2 }>;
// Result: { a: 1; b: 2 }

type Test2 = SafeMerge<string | number>;
// Result: string | number (stays union)
```

---

#### Alternative Approaches

**1. Using Reduce-Like Pattern:**

```typescript
// For known tuple types, you can use indexed access
type Merge2<A, B> = A & B;
type Merge3<A, B, C> = A & B & C;
type Merge4<A, B, C, D> = A & B & C & D;

// Or use recursive type
type MergeTuple<T extends any[]> = 
  T extends [infer First, ...infer Rest]
    ? First & MergeTuple<Rest>
    : unknown;

type Result = MergeTuple<[{ a: 1 }, { b: 2 }, { c: 3 }]>;
// Result: { a: 1 } & { b: 2 } & { c: 3 }
```

**2. Manual Intersection:**

```typescript
// For specific cases, manual intersection is clearer
type A = { a: string };
type B = { b: number };
type C = { c: boolean };

// Manual
type Manual = A & B & C;

// Generic (less obvious)
type Generic = UnionToIntersection<A | B | C>;

// Both produce: { a: string; b: number; c: boolean }
// Manual is more readable for fixed types
```

---

#### Summary

**UnionToIntersection Type:**

```typescript
// The complete utility type
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// Usage
type Union = { a: string } | { b: number } | { c: boolean };
type Intersection = UnionToIntersection<Union>;
// Result: { a: string; b: number; c: boolean }
```

**Key Concepts:**
- 🔄 Converts union (`|`) to intersection (`&`)
- 📦 Uses contravariance of function parameters
- 🎯 Works with object types, not primitives
- ⚡ Enables advanced type manipulation
- 🔧 Common in utility type libraries
- 🎨 Useful for merging configurations
- 🧩 Essential for plugin systems
- 📊 Great for Redux/state management

**When to Use:**
- ✅ Merging multiple object types
- ✅ Plugin/mixin systems
- ✅ Configuration merging
- ✅ State management types
- ✅ API response combining
- ✅ Event handler merging
- ❌ Not for primitive types
- ❌ Not when properties conflict

**Common Pattern:**

```typescript
// 1. Define utility
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends 
  (k: infer I) => void ? I : never;

// 2. Create union
type MyUnion = TypeA | TypeB | TypeC;

// 3. Convert to intersection
type MyIntersection = UnionToIntersection<MyUnion>;

// 4. Use the merged type
const merged: MyIntersection = {
  // All properties from TypeA, TypeB, and TypeC
};
```

---

### 14. Explain Conditional Types
Conditional types select one of two possible types based on a condition.

```typescript
type IsString<T> = T extends string ? true : false;
```

### 15. What is `never` type used for?
- Functions that never return (throw errors or infinite loops)
- Exhaustive type checking
- Representing impossible states

```typescript
function throwError(message: string): never {
    throw new Error(message);
}
```

### 15. What are Mapped Types?
Mapped types transform properties of an existing type.

```typescript
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};
```

### 16. Explain the `infer` keyword
`infer` is used in conditional types to infer types within the condition.

```typescript
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;
```

### 17. What are Declaration Files (.d.ts)?
Declaration files contain type information for JavaScript libraries. They provide type definitions without implementation.

### 18. What is the difference between `unknown` and `any`?
- `any`: Disables type checking
- `unknown`: Type-safe version of any; requires type checking before use

### 19. Explain Abstract Classes
Abstract classes cannot be instantiated directly and serve as base classes.

```typescript
abstract class Animal {
    abstract makeSound(): void;
    
    move(): void {
        console.log("Moving...");
    }
}
```

### 20. What are Decorators?
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

### 21. What is Type Assertion?
Type assertion tells TypeScript to treat a value as a specific type.

```typescript
let someValue: unknown = "this is a string";
let strLength: number = (someValue as string).length;
// or
let strLength2: number = (<string>someValue).length;
```

## Configuration

### 22. Important tsconfig.json options
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

### 23. What does `strict` mode enable?
- `noImplicitAny`
- `strictNullChecks`
- `strictFunctionTypes`
- `strictBindCallApply`
- `strictPropertyInitialization`
- `noImplicitThis`
- `alwaysStrict`

## Best Practices

### 24. TypeScript Best Practices
- Use strict mode
- Avoid `any` type when possible
- Use interfaces for public APIs
- Leverage type inference
- Use const assertions for literal types
- Prefer union types over enums for simple cases
- Use utility types to avoid repetition
- Enable all strict flags in tsconfig.json
