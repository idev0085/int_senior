# JavaScript Interview Questions - Complete Guide

## Table of Contents
1. [JavaScript Basics (1-60)](#javascript-basics)
2. [Intermediate JavaScript (61-140)](#intermediate-javascript)
3. [Advanced JavaScript (141-230)](#advanced-javascript)
4. [Lead/Architect Level (231-300)](#lead-architect-level)

---

## JavaScript Basics

### Q1: What is JavaScript?

**Answer:**
JavaScript is a high-level, interpreted programming language that enables interactive web pages. It's a core technology of the web alongside HTML and CSS.

**Key Characteristics:**
- **Interpreted**: Runs directly in the browser without compilation
- **Dynamically Typed**: Variables can hold any type of data
- **Single-threaded**: Executes one task at a time
- **Event-driven**: Responds to user actions
- **Prototype-based**: Uses prototypal inheritance

```javascript
// JavaScript runs in browser or Node.js
console.log('Hello, World!');

// Dynamic typing
let x = 5;      // number
x = "hello";    // now string (no error!)

// Event-driven
button.addEventListener('click', () => {
  console.log('Button clicked!');
});
```

### Q2: Is JavaScript compiled or interpreted?

**Answer:**
JavaScript is **interpreted**, but modern engines use **JIT (Just-In-Time) compilation** for optimization.

**How it works:**
```
Source Code → Parser → AST → Interpreter → Bytecode
                                ↓
                         JIT Compiler → Optimized Machine Code
```

**Traditional interpretation:**
- Code executed line by line
- Slower performance

**Modern JIT compilation:**
- Code is compiled to bytecode
- Hot code paths compiled to machine code
- V8, SpiderMonkey use JIT

### Q3: Difference between JavaScript and Java

| Feature | JavaScript | Java |
|---------|-----------|------|
| Type | Scripting language | Programming language |
| Execution | Interpreted (JIT) | Compiled to bytecode |
| Typing | Dynamic, weak | Static, strong |
| Platform | Browser, Node.js | JVM (cross-platform) |
| OOP | Prototype-based | Class-based |
| Concurrency | Event loop, async | Multi-threaded |
| Use Case | Web development | Enterprise, Android |

### Q4: What are JavaScript data types?

**Answer:**

**Primitive Types (7):**
```javascript
// 1. String
let name = "Alice";
let template = `Hello ${name}`;

// 2. Number
let age = 25;
let pi = 3.14;
let big = 1e10;

// 3. BigInt (for very large integers)
let huge = 9007199254740991n;

// 4. Boolean
let isActive = true;

// 5. Undefined
let notAssigned;
console.log(notAssigned); // undefined

// 6. Null
let empty = null;

// 7. Symbol (unique identifier)
let id = Symbol('id');
```

**Reference Types:**
```javascript
// Object
let person = { name: 'John', age: 30 };

// Array
let numbers = [1, 2, 3, 4, 5];

// Function
function greet() { return 'Hello'; }

// Date
let now = new Date();

// RegExp
let pattern = /abc/;
```

### Q5: What is the typeof operator?

**Answer:**
`typeof` returns a string indicating the type of a value.

```javascript
typeof "hello"              // "string"
typeof 42                   // "number"
typeof 3.14                 // "number"
typeof true                 // "boolean"
typeof undefined            // "undefined"
typeof Symbol()             // "symbol"
typeof 123n                 // "bigint"

// Reference types
typeof { name: 'John' }     // "object"
typeof [1, 2, 3]           // "object" ⚠️
typeof null                // "object" ⚠️ (historical bug)
typeof function() {}       // "function"

// Check for array
Array.isArray([1, 2, 3])   // true

// Check for null
value === null             // correct way
```

### Q6: What is undefined?

**Answer:**
`undefined` is a primitive value automatically assigned to variables that have been declared but not initialized.

```javascript
// Variable declared but not assigned
let x;
console.log(x); // undefined

// Function with no return
function doNothing() {}
console.log(doNothing()); // undefined

// Object property that doesn't exist
let obj = { name: 'John' };
console.log(obj.age); // undefined

// Function parameter not provided
function greet(name) {
  console.log(name);
}
greet(); // undefined

// Array index out of bounds
let arr = [1, 2, 3];
console.log(arr[10]); // undefined
```

### Q7: What is null?

**Answer:**
`null` is a primitive value that represents the intentional absence of any object value.

```javascript
// Explicitly assigned to indicate "no value"
let user = null; // User not logged in

// Common use cases
let data = null; // Data not yet loaded

// API response
if (response.data === null) {
  console.log('No data found');
}

// Clear a value
let obj = { name: 'John' };
obj = null; // Clear reference (garbage collection)
```

### Q8: Difference between null and undefined

| Feature | undefined | null |
|---------|-----------|------|
| Type | "undefined" | "object" (legacy bug) |
| Assigned by | JavaScript automatically | Developer explicitly |
| Meaning | Variable not initialized | Intentional absence |
| Default | Function params, variables | Must be assigned |

```javascript
// undefined - automatic
let x;
console.log(x); // undefined
typeof x;       // "undefined"

// null - intentional
let y = null;
console.log(y); // null
typeof y;       // "object" (bug!)

// Comparison
undefined == null   // true (loose equality)
undefined === null  // false (strict equality)

// Check for both
if (value == null) { // Checks for both null and undefined
  console.log('No value');
}
```

### Q9: What is variable hoisting?

**Answer:**
Hoisting is JavaScript's behavior of moving declarations to the top of their scope during compilation.

```javascript
// var hoisting
console.log(x); // undefined (not error!)
var x = 5;

// Interpreted as:
var x;           // Declaration hoisted
console.log(x);  // undefined
x = 5;          // Initialization stays

// let/const hoisting (temporal dead zone)
console.log(y); // ReferenceError!
let y = 10;

// Function hoisting
greet(); // Works! "Hello"
function greet() {
  console.log('Hello');
}

// Function expression NOT hoisted
sayHi(); // Error!
var sayHi = function() {
  console.log('Hi');
};
```

### Q10: Difference between var, let, and const

| Feature | var | let | const |
|---------|-----|-----|-------|
| Scope | Function | Block | Block |
| Hoisting | Yes (undefined) | Yes (TDZ) | Yes (TDZ) |
| Re-declaration | Allowed | Not allowed | Not allowed |
| Re-assignment | Allowed | Allowed | Not allowed |
| Temporal Dead Zone | No | Yes | Yes |

```javascript
// var - function scoped
function varExample() {
  if (true) {
    var x = 10;
  }
  console.log(x); // 10 (accessible outside block)
}

// let - block scoped
function letExample() {
  if (true) {
    let y = 10;
  }
  console.log(y); // ReferenceError!
}

// const - cannot reassign
const PI = 3.14;
PI = 3.15; // TypeError!

// But can mutate objects
const obj = { name: 'John' };
obj.name = 'Jane';  // ✅ Allowed
obj = {};           // ❌ TypeError!

const arr = [1, 2, 3];
arr.push(4);        // ✅ Allowed
arr = [];           // ❌ TypeError!
```

### Q11: What is scope in JavaScript?

**Answer:**
Scope determines the accessibility of variables in different parts of the code.

**Types of Scope:**

**1. Global Scope:**
```javascript
// Available everywhere
var globalVar = 'I am global';
let globalLet = 'Also global';

function test() {
  console.log(globalVar); // Accessible
}
```

**2. Function Scope:**
```javascript
function myFunction() {
  var functionScoped = 'Only inside function';
  console.log(functionScoped); // Works
}

console.log(functionScoped); // ReferenceError!
```

**3. Block Scope (let/const):**
```javascript
if (true) {
  let blockScoped = 'Only in block';
  const alsoBlockScoped = 'Same here';
  var notBlockScoped = 'I escape!';
}

console.log(blockScoped);     // ReferenceError!
console.log(alsoBlockScoped); // ReferenceError!
console.log(notBlockScoped);  // Works (var ignores blocks)
```

**4. Lexical Scope:**
```javascript
function outer() {
  let outerVar = 'outer';
  
  function inner() {
    console.log(outerVar); // Accesses parent scope
  }
  
  inner();
}
```

### Q12: What is an IIFE?

**Answer:**
IIFE (Immediately Invoked Function Expression) is a function that runs as soon as it's defined.

```javascript
// Basic IIFE
(function() {
  console.log('Invoked immediately!');
})();

// Arrow function IIFE
(() => {
  console.log('Arrow IIFE');
})();

// Named IIFE
(function namedIIFE() {
  console.log('Named IIFE');
})();

// With parameters
(function(name) {
  console.log(`Hello, ${name}`);
})('Alice');

// Return value
const result = (function() {
  return 42;
})();
console.log(result); // 42
```

**Use cases:**
```javascript
// 1. Avoid polluting global scope
(function() {
  var private = 'Not global';
})();
// console.log(private); // Error!

// 2. Module pattern
const module = (function() {
  let privateVar = 0;
  
  return {
    increment: () => privateVar++,
    getValue: () => privateVar
  };
})();

module.increment();
console.log(module.getValue()); // 1

// 3. Async IIFE
(async () => {
  const data = await fetchData();
  console.log(data);
})();
```

### Q13: What is the this keyword?

**Answer:**
`this` is a special keyword that points to the **owner** of the function you're running. Think of it as "who is calling this function?" The answer changes based on **how** you call it.

```javascript
// 1. Global context
console.log(this); // Window (browser) or global (Node.js)

// 2. Object method
const person = {
  name: 'Alice',
  greet: function() {
    console.log(`Hello, ${this.name}`);
  }
};
person.greet(); // "Hello, Alice" (this = person)

// 3. Regular function
function showThis() {
  console.log(this);
}
showThis(); // Window (or undefined in strict mode)

// 4. Arrow function (lexical this)
const obj = {
  name: 'Bob',
  greet: () => {
    console.log(this.name); // undefined (this from parent scope)
  }
};

// 5. Constructor function
function Person(name) {
  this.name = name;
}
const p = new Person('Charlie');
console.log(p.name); // "Charlie"

// 6. Event handler
button.addEventListener('click', function() {
  console.log(this); // The button element
});

// 7. Explicit binding
function greet() {
  console.log(this.name);
}
const user = { name: 'David' };
greet.call(user);    // "David"
greet.apply(user);   // "David"
const bound = greet.bind(user);
bound();             // "David"
```

### Q14: What is NaN?

**Answer:**
`NaN` (Not-a-Number) is a special numeric value representing an invalid number.

```javascript
// Operations that produce NaN
console.log(0 / 0);           // NaN
console.log(Math.sqrt(-1));   // NaN
console.log(parseInt('abc')); // NaN
console.log('hello' * 2);     // NaN

// NaN is not equal to anything, including itself!
console.log(NaN === NaN);     // false ⚠️

// How to check for NaN
console.log(isNaN(NaN));          // true
console.log(isNaN('hello'));      // true (coerces to NaN)

// Better way: Number.isNaN() (no coercion)
console.log(Number.isNaN(NaN));      // true
console.log(Number.isNaN('hello'));  // false (not coerced)

// typeof NaN
console.log(typeof NaN); // "number" ⚠️
```

### Q15: What are truthy and falsy values?

**Answer:**

**Falsy values (8 total):**
```javascript
// These evaluate to false in boolean context
false
0
-0
0n          // BigInt zero
""          // empty string
null
undefined
NaN

// Examples
if (0) {
  // Never runs
}

if ("") {
  // Never runs
}

Boolean(null);      // false
Boolean(undefined); // false
Boolean(NaN);       // false
```

**Truthy values:**
```javascript
// Everything else is truthy!
true
1
-1
"hello"
"0"         // ⚠️ String "0" is truthy
"false"     // ⚠️ String "false" is truthy
[]          // Empty array is truthy
{}          // Empty object is truthy
function(){}

// Examples
if ("0") {
  // Runs! "0" is truthy
}

if ([]) {
  // Runs! [] is truthy
}

Boolean("false"); // true
Boolean([]);      // true
Boolean({});      // true
```

### Q16: What is type coercion?

**Answer:**
Type coercion is the automatic or implicit conversion of values from one data type to another.

**Implicit coercion:**
```javascript
// String coercion
'5' + 2        // "52" (number → string)
'5' + true     // "5true"
'5' + null     // "5null"

// Number coercion
'5' - 2        // 3 (string → number)
'5' * '2'      // 10
'5' / 2        // 2.5
true + 1       // 2 (true → 1)
false + 1      // 1 (false → 0)

// Boolean coercion
if ('hello') { } // "hello" → true
if (0) { }       // 0 → false

// Equality coercion
5 == '5'       // true (loose equality)
null == undefined // true
```

**Explicit coercion:**
```javascript
// To string
String(123)         // "123"
(123).toString()    // "123"
123 + ""            // "123"

// To number
Number("123")       // 123
parseInt("123")     // 123
parseFloat("12.34") // 12.34
+"123"              // 123 (unary plus)

// To boolean
Boolean(1)          // true
Boolean(0)          // false
!!1                 // true (double negation)
```

### Q17: What are template literals?

**Answer:**
Template literals are string literals allowing embedded expressions and multi-line strings.

```javascript
// Basic usage
const name = 'Alice';
const greeting = `Hello, ${name}!`; // "Hello, Alice!"

// Expressions
const a = 5, b = 10;
console.log(`Sum: ${a + b}`); // "Sum: 15"

// Function calls
function upper(str) {
  return str.toUpperCase();
}
console.log(`Name: ${upper(name)}`); // "Name: ALICE"

// Multi-line strings
const html = `
  <div>
    <h1>Title</h1>
    <p>Paragraph</p>
  </div>
`;

// Nested templates
const nested = `Outer ${`Inner ${1 + 1}`}`;

// Tagged templates
function tag(strings, ...values) {
  console.log(strings); // ["Hello ", "!"]
  console.log(values);  // ["Alice"]
}
tag`Hello ${name}!`;
```

### Q18: Difference between == and ===

**Answer:**

| Operator | Name | Type Conversion | Example |
|----------|------|----------------|---------|
| == | Loose equality | Yes | 5 == '5' → true |
| === | Strict equality | No | 5 === '5' → false |

```javascript
// Loose equality (==) - performs type coercion
5 == '5'           // true
1 == true          // true
0 == false         // true
null == undefined  // true
'' == 0            // true

// Strict equality (===) - no coercion
5 === '5'          // false
1 === true         // false
0 === false        // false
null === undefined // false
'' === 0           // false

// Always use === unless you specifically need coercion
const value = getUserInput();
if (value === null) {  // ✅ Explicit
  // Handle null
}

// Exception: checking for null/undefined
if (value == null) {   // ✅ Checks both null and undefined
  // value is null or undefined
}
```

### Q19: What is short-circuit evaluation?

**Answer:**
Short-circuit evaluation stops evaluating as soon as the result is determined.

```javascript
// AND (&&) - stops at first falsy
true && true && false && console.log('Never runs');

// OR (||) - stops at first truthy
true || console.log('Never runs');

// Common patterns

// 1. Default values (before nullish coalescing)
const name = userName || 'Guest';

// 2. Conditional execution
isLoggedIn && redirectToDashboard();

// 3. Nested property access
const city = user && user.address && user.address.city;

// 4. Guard clauses
function process(data) {
  data || return; // Return if falsy
  // Process data
}

// Nullish coalescing (??) - only null/undefined
const value = input ?? 'default'; // Better for 0, false, ""

// Optional chaining (?.)
const city = user?.address?.city; // Cleaner nested access
```

### Q20: What is strict mode?

**Answer:**
Strict mode is a way to opt into a restricted variant of JavaScript.

```javascript
// Enable for entire script
'use strict';

// Enable for function
function strictFunc() {
  'use strict';
  // Strict mode rules apply here
}

// What strict mode prevents:

// 1. Accidental globals
'use strict';
x = 10; // ReferenceError (without var/let/const)

// 2. Deleting variables
'use strict';
let x = 10;
delete x; // SyntaxError

// 3. Duplicate parameters
'use strict';
function sum(a, a, c) { // SyntaxError
  return a + a + c;
}

// 4. Octal literals
'use strict';
let num = 010; // SyntaxError

// 5. Writing to read-only properties
'use strict';
const obj = {};
Object.defineProperty(obj, 'x', { value: 42, writable: false });
obj.x = 9; // TypeError

// 6. this in functions is undefined
'use strict';
function showThis() {
  console.log(this); // undefined (not Window)
}
showThis();
```

### Q21: What is an array?

**Answer:**
An array is an ordered collection of values accessible by numeric indices.

```javascript
// Creation
const arr1 = [1, 2, 3, 4, 5];
const arr2 = new Array(5); // [empty × 5]
const arr3 = Array.of(1, 2, 3); // [1, 2, 3]
const arr4 = Array.from('hello'); // ['h','e','l','l','o']

// Mixed types
const mixed = [1, 'hello', true, null, { name: 'John' }];

// Indexing
console.log(arr1[0]);  // 1
console.log(arr1[4]);  // 5
arr1[2] = 10;          // Modify
console.log(arr1);     // [1, 2, 10, 4, 5]

// Length
console.log(arr1.length); // 5

// Common operations
arr1.push(6);        // Add to end
arr1.pop();          // Remove from end
arr1.unshift(0);     // Add to start
arr1.shift();        // Remove from start

// Iteration
arr1.forEach(item => console.log(item));
for (let item of arr1) {
  console.log(item);
}
```

### Q22: What is slice() vs splice()?

**Answer:**

**slice() - Copies portion (doesn't modify original):**
```javascript
const fruits = ['Apple', 'Banana', 'Cherry', 'Date'];

// array.slice(startIndex, endIndex)
const part = fruits.slice(1, 3);
console.log(part);   // ["Banana", "Cherry"]
console.log(fruits); // ["Apple", "Banana", "Cherry", "Date"] ✅ Unchanged

// Negative indices
fruits.slice(-2);    // ["Cherry", "Date"] (last 2)

// Copy entire array
const copy = fruits.slice();
```

**splice() - Modifies original array:**
```javascript
const fruits = ['Apple', 'Banana', 'Cherry'];

// array.splice(index, deleteCount, item1, item2, ...)
const removed = fruits.splice(1, 1, 'Mango');

console.log(removed); // ["Banana"] (deleted items)
console.log(fruits);  // ["Apple", "Mango", "Cherry"] ✅ Modified

// Insert without deleting
fruits.splice(1, 0, 'Orange'); // Insert at index 1
// ["Apple", "Orange", "Mango", "Cherry"]

// Delete without inserting
fruits.splice(2, 1); // Delete 1 at index 2
// ["Apple", "Orange", "Cherry"]
```

**Comparison:**

| Feature | slice | splice |
|---------|-------|--------|
| Modifies original | ❌ No | ✅ Yes |
| Returns | New array | Deleted items |
| Purpose | Copy portion | Add/remove items |

### Q23: What is join() and split()?

**Answer:**

**join() - Array → String:**
```javascript
const fruits = ["Apple", "Banana", "Cherry"];

console.log(fruits.join());       // "Apple,Banana,Cherry" (default)
console.log(fruits.join(" "));    // "Apple Banana Cherry"
console.log(fruits.join("-"));    // "Apple-Banana-Cherry"
console.log(fruits.join(""));     // "AppleBananaCherry"

// Use case: Create CSV
const csv = data.map(row => row.join(',')).join('\n');
```

**split() - String → Array:**
```javascript
const str = "Apple-Banana-Cherry";

console.log(str.split("-"));      // ["Apple", "Banana", "Cherry"]
console.log(str.split(""));       // ["A","p","p","l","e",...]
console.log(str.split(" "));      // ["Apple-Banana-Cherry"]

// Limit results
"a-b-c-d".split("-", 2);         // ["a", "b"]

// Use case: Parse CSV
const csvLine = "John,Doe,30";
const [firstName, lastName, age] = csvLine.split(",");
```

### Q24: What is includes() and indexOf()?

**Answer:**

**includes() - Check existence (returns boolean):**
```javascript
const pets = ["cat", "dog", "bat"];

console.log(pets.includes("cat"));      // true
console.log(pets.includes("at"));       // false (exact match)
console.log(pets.includes("cat", 1));   // false (start from index 1)

// With strings
"hello world".includes("world");        // true
"hello world".includes("World");        // false (case-sensitive)

// Check for NaN
[1, 2, NaN].includes(NaN);              // true ✅
```

**indexOf() - Find index (returns number):**
```javascript
const arr = ['a', 'b', 'c', 'b'];

console.log(arr.indexOf('b'));          // 1 (first occurrence)
console.log(arr.indexOf('d'));          // -1 (not found)
console.log(arr.indexOf('b', 2));       // 3 (start from index 2)

// With strings
"hello".indexOf('l');                   // 2
"hello".indexOf('x');                   // -1

// Check for NaN
[1, 2, NaN].indexOf(NaN);               // -1 ❌ (can't find NaN)

// Use case: Check if exists
if (arr.indexOf('b') !== -1) {
  console.log('Found');
}

// Better with includes
if (arr.includes('b')) {
  console.log('Found');
}
```

---

## Intermediate JavaScript

### Q25: What is an object in JavaScript?

**Answer:**
An object is a collection of key-value pairs (properties and methods).

**Creating objects:**

**1. Object Literal (most common):**
```javascript
const person = {
  firstName: "John",
  lastName: "Doe",
  age: 30,
  greet: function() {
    return `Hello, ${this.firstName}`;
  }
};
```

**2. Constructor Function:**
```javascript
function Person(first, last, age) {
  this.firstName = first;
  this.lastName = last;
  this.age = age;
  this.greet = function() {
    return `Hello, ${this.firstName}`;
  };
}

const person1 = new Person("Jane", "Smith", 25);
```

**3. Class (ES6):**
```javascript
class Person {
  constructor(first, last, age) {
    this.firstName = first;
    this.lastName = last;
    this.age = age;
  }
  
  greet() {
    return `Hello, ${this.firstName}`;
  }
}

const person2 = new Person("Alice", "Johnson", 28);
```

**4. Object.create():**
```javascript
const personProto = {
  greet() {
    return `Hello, ${this.firstName}`;
  }
};

const person3 = Object.create(personProto);
person3.firstName = "Bob";
person3.lastName = "Brown";
```

### Q26: What is Object.freeze() vs Object.seal()?

**Answer:**

**Object.freeze() - Complete immutability:**
```javascript
'use strict';

const user = {
  name: 'John',
  age: 30
};

Object.freeze(user);

// All modifications fail
user.name = 'Doe';      // TypeError
user.gender = 'male';   // TypeError
delete user.age;        // TypeError

console.log(user);      // { name: 'John', age: 30 }

// Check if frozen
Object.isFrozen(user);  // true

// Shallow freeze only
const nested = {
  user: { name: 'John' }
};
Object.freeze(nested);
nested.user.name = 'Jane'; // ✅ Works! (nested not frozen)
```

**Object.seal() - Can modify, can't add/delete:**
```javascript
const user = {
  name: 'John',
  age: 30
};

Object.seal(user);

// Can modify
user.name = 'Jane';     // ✅ Works
user.age = 25;          // ✅ Works

// Can't add
user.gender = 'male';   // TypeError

// Can't delete
delete user.age;        // TypeError

console.log(user);      // { name: 'Jane', age: 25 }

// Check if sealed
Object.isSealed(user);  // true
```

**Comparison:**

| Feature | Object.preventExtensions() | Object.seal() | Object.freeze() |
|---------|---------------------------|---------------|-----------------|
| Add properties | ❌ | ❌ | ❌ |
| Delete properties | ✅ | ❌ | ❌ |
| Modify values | ✅ | ✅ | ❌ |
| Change descriptors | ✅ | ❌ | ❌ |

### Q27: What is shallow copy vs deep copy?

**Answer:**

**Shallow Copy - Top level only:**
```javascript
const original = {
  name: 'John',
  age: 30,
  address: {
    city: 'New York',
    zip: '10001'
  }
};

// Method 1: Spread operator
const copy1 = { ...original };

// Method 2: Object.assign()
const copy2 = Object.assign({}, original);

// Modify shallow properties - safe
copy1.name = 'Jane';
console.log(original.name); // 'John' ✅ Original unchanged

// Modify nested properties - PROBLEM!
copy1.address.city = 'Boston';
console.log(original.address.city); // 'Boston' ❌ Original changed!
```

**Deep Copy - All levels:**
```javascript
// Method 1: structuredClone() (modern, recommended)
const deepCopy1 = structuredClone(original);
deepCopy1.address.city = 'Boston';
console.log(original.address.city); // 'New York' ✅

// Method 2: JSON (limitations: loses functions, dates, undefined)
const deepCopy2 = JSON.parse(JSON.stringify(original));

// Method 3: Custom recursive function
function deepClone(obj) {
  if (obj === null || typeof obj !== 'object') return obj;
  
  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof Array) return obj.map(item => deepClone(item));
  
  const cloned = {};
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      cloned[key] = deepClone(obj[key]);
    }
  }
  return cloned;
}

// Method 4: Lodash
const _ = require('lodash');
const deepCopy3 = _.cloneDeep(original);
```

**Which to use:**

| Use Case | Recommended Method |
|----------|-------------------|
| Deep copy (modern) | `structuredClone()` |
| Shallow copy | `{ ...obj }` |
| With functions | Lodash `_.cloneDeep()` |
| Legacy browsers | JSON or custom recursive |

### Q28: What is JSON?

**Answer:**
JSON (JavaScript Object Notation) is a lightweight data-interchange format.

**JSON vs JavaScript Object:**

| Feature | JSON | JS Object |
|---------|------|-----------|
| Keys | Must be strings | Can be any type |
| Values | String, number, boolean, array, object, null | Any type including functions |
| Quotes | Double quotes only | Optional on keys |
| Trailing commas | ❌ Not allowed | ✅ Allowed |

```javascript
// JavaScript Object
const jsObj = {
  name: 'John',
  age: 30,
  greet: function() { return 'Hello'; }, // ✅ Function allowed
  date: new Date(), // ✅ Date allowed
  undefined: undefined // ✅ undefined allowed
};

// JSON (as string)
const jsonString = `{
  "name": "John",
  "age": 30
}`;

// Convert: Object → JSON string
const json = JSON.stringify(jsObj);
console.log(json);
// {"name":"John","age":30,"date":"2024-01-20T..."}
// Note: function lost, undefined lost

// Convert: JSON string → Object
const parsed = JSON.parse(jsonString);
console.log(parsed); // { name: 'John', age: 30 }

// Pretty print JSON
JSON.stringify(obj, null, 2); // Indent with 2 spaces

// Filter properties
JSON.stringify(obj, ['name', 'age']); // Only these props
```

### Q29: What are higher-order functions?

**Answer:**
A higher-order function is a function that either:
1. Takes a function as an argument, OR
2. Returns a function

```javascript
// 1. Takes function as argument
function higherOrder(callback) {
  callback();
}

higherOrder(() => console.log('Hello'));

// Built-in examples
[1, 2, 3].map(x => x * 2);        // map is HOF
[1, 2, 3].filter(x => x > 1);     // filter is HOF
[1, 2, 3].reduce((a, b) => a + b); // reduce is HOF

// 2. Returns a function
function multiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = multiplier(2);
const triple = multiplier(3);

console.log(double(5));  // 10
console.log(triple(5));  // 15

// Real-world example: Logger
function createLogger(prefix) {
  return function(message) {
    console.log(`[${prefix}] ${message}`);
  };
}

const errorLog = createLogger('ERROR');
const infoLog = createLogger('INFO');

errorLog('Something went wrong'); // [ERROR] Something went wrong
infoLog('Server started');        // [INFO] Server started
```

### Q30: What is a callback function?

**Answer:**
A callback is a function passed as an argument to another function, to be executed later.

```javascript
// Simple callback
function greeting(name, callback) {
  console.log(`Hello, ${name}`);
  callback();
}

greeting('Alice', function() {
  console.log('Callback executed!');
});

// Async callbacks
setTimeout(() => {
  console.log('Runs after 1 second');
}, 1000);

// Event callbacks
button.addEventListener('click', function() {
  console.log('Button clicked!');
});

// Array methods
const numbers = [1, 2, 3, 4, 5];

numbers.forEach(function(num) {
  console.log(num);
});

const doubled = numbers.map(function(num) {
  return num * 2;
});

// Error-first callbacks (Node.js pattern)
fs.readFile('file.txt', function(err, data) {
  if (err) {
    console.error('Error:', err);
    return;
  }
  console.log('Data:', data);
});
```

### Q31: What is callback hell?

**Answer:**
Callback hell is the pyramid of doom created by nesting multiple callbacks.

```javascript
// ❌ Callback hell
getData(function(a) {
  getMoreData(a, function(b) {
    getMoreData(b, function(c) {
      getMoreData(c, function(d) {
        getMoreData(d, function(e) {
          console.log('Finally done!');
        });
      });
    });
  });
});

// ✅ Solution 1: Promises
getData()
  .then(a => getMoreData(a))
  .then(b => getMoreData(b))
  .then(c => getMoreData(c))
  .then(d => getMoreData(d))
  .then(e => console.log('Done!'))
  .catch(err => console.error(err));

// ✅ Solution 2: Async/Await
async function fetchData() {
  try {
    const a = await getData();
    const b = await getMoreData(a);
    const c = await getMoreData(b);
    const d = await getMoreData(c);
    const e = await getMoreData(d);
    console.log('Done!');
  } catch (err) {
    console.error(err);
  }
}
```

### Q32: What is the event loop?

**Answer:**
The event loop is the mechanism that handles asynchronous operations in JavaScript.

**How it works:**
```
┌───────────────────────────┐
│      Call Stack           │ ← Synchronous code
└───────────────────────────┘
            ↓
┌───────────────────────────┐
│    Microtask Queue        │ ← Promises, queueMicrotask
└───────────────────────────┘
            ↓
┌───────────────────────────┐
│    Macrotask Queue        │ ← setTimeout, setInterval
└───────────────────────────┘
```

**Example:**
```javascript
console.log('1'); // Sync

setTimeout(() => {
  console.log('2'); // Macrotask
}, 0);

Promise.resolve().then(() => {
  console.log('3'); // Microtask
});

console.log('4'); // Sync

// Output: 1, 4, 3, 2

// Execution order:
// 1. Synchronous code (1, 4)
// 2. Microtasks (3)
// 3. Macrotasks (2)
```

**Detailed example:**
```javascript
console.log('Start');

setTimeout(() => console.log('Timeout 1'), 0);

Promise.resolve()
  .then(() => console.log('Promise 1'))
  .then(() => console.log('Promise 2'));

setTimeout(() => console.log('Timeout 2'), 0);

console.log('End');

// Output:
// Start
// End
// Promise 1
// Promise 2
// Timeout 1
// Timeout 2
```

### Q33: What is the microtask queue vs macrotask queue?

**Answer:**

**Microtasks (higher priority):**
- Promises (.then, .catch, .finally)
- queueMicrotask()
- MutationObserver
- Process.nextTick (Node.js)

**Macrotasks (lower priority):**
- setTimeout
- setInterval
- setImmediate (Node.js)
- I/O operations
- UI rendering

**Execution order:**
```javascript
setTimeout(() => console.log('Macrotask 1'), 0);

Promise.resolve()
  .then(() => {
    console.log('Microtask 1');
    setTimeout(() => console.log('Macrotask 2'), 0);
  })
  .then(() => console.log('Microtask 2'));

queueMicrotask(() => console.log('Microtask 3'));

setTimeout(() => console.log('Macrotask 3'), 0);

// Output:
// Microtask 1
// Microtask 2
// Microtask 3
// Macrotask 1
// Macrotask 3
// Macrotask 2

// Flow:
// 1. All sync code completes
// 2. Process ALL microtasks
// 3. Process ONE macrotask
// 4. Back to step 2 (check microtasks again)
```

### Q34: What are Promises?

**Answer:**
A Promise is an object representing the eventual completion or failure of an asynchronous operation.

**Promise States:**
- **Pending**: Initial state
- **Fulfilled**: Operation completed successfully
- **Rejected**: Operation failed

```javascript
// Creating a promise
const promise = new Promise((resolve, reject) => {
  // Async operation
  setTimeout(() => {
    const success = true;
    if (success) {
      resolve('Success!');
    } else {
      reject('Error!');
    }
  }, 1000);
});

// Consuming a promise
promise
  .then(result => {
    console.log(result); // 'Success!'
    return 'Next value';
  })
  .then(value => {
    console.log(value); // 'Next value'
  })
  .catch(error => {
    console.error(error);
  })
  .finally(() => {
    console.log('Always runs');
  });

// Real example: Fetch API
fetch('https://api.example.com/data')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));
```

**Promise methods:**
```javascript
// Promise.all() - Wait for all
Promise.all([promise1, promise2, promise3])
  .then(results => {
    // results = [result1, result2, result3]
  })
  .catch(error => {
    // If ANY promise rejects
  });

// Promise.race() - First to settle
Promise.race([promise1, promise2])
  .then(result => {
    // Result of first resolved promise
  });

// Promise.any() - First to fulfill
Promise.any([promise1, promise2])
  .then(result => {
    // First successful result
  });

// Promise.allSettled() - Wait for all (regardless of result)
Promise.allSettled([promise1, promise2])
  .then(results => {
    // results = [
    //   { status: 'fulfilled', value: ... },
    //   { status: 'rejected', reason: ... }
    // ]
  });
```

### Q35: What is async/await?

**Answer:**
`async/await` is syntactic sugar over Promises, making asynchronous code look synchronous.

```javascript
// Without async/await (Promises)
function fetchData() {
  return fetch('https://api.example.com/data')
    .then(response => response.json())
    .then(data => {
      console.log(data);
      return data;
    })
    .catch(error => {
      console.error(error);
    });
}

// With async/await
async function fetchData() {
  try {
    const response = await fetch('https://api.example.com/data');
    const data = await response.json();
    console.log(data);
    return data;
  } catch (error) {
    console.error(error);
  }
}

// Sequential execution
async function sequential() {
  const data1 = await fetchData1(); // Wait
  const data2 = await fetchData2(); // Then wait
  return [data1, data2];
}

// Parallel execution
async function parallel() {
  const [data1, data2] = await Promise.all([
    fetchData1(), // Start both
    fetchData2()  // at same time
  ]);
  return [data1, data2];
}

// Error handling
async function withErrorHandling() {
  try {
    const data = await fetchData();
    return data;
  } catch (error) {
    if (error.status === 404) {
      console.log('Not found');
    } else {
      throw error; // Re-throw other errors
    }
  } finally {
    console.log('Cleanup');
  }
}

// Top-level await (modern browsers/Node 14.8+)
const data = await fetchData(); // No async function needed
```

### Q36: What are closures?

**Answer:**
A closure is a function that retains access to its lexical scope even when executed outside that scope.

```javascript
// Basic closure
function outer() {
  let count = 0; // Private variable
  
  function inner() {
    count++;
    console.log(count);
  }
  
  return inner;
}

const counter = outer();
counter(); // 1
counter(); // 2
counter(); // 3
// count is preserved!

// Real-world use cases

// 1. Data privacy / encapsulation
function createBankAccount(initialBalance) {
  let balance = initialBalance; // Private
  
  return {
    deposit(amount) {
      balance += amount;
      return balance;
    },
    withdraw(amount) {
      if (amount <= balance) {
        balance -= amount;
        return balance;
      }
      return 'Insufficient funds';
    },
    getBalance() {
      return balance;
    }
  };
}

const account = createBankAccount(1000);
account.deposit(500);   // 1500
account.withdraw(200);  // 1300
account.getBalance();   // 1300
// account.balance is not accessible!

// 2. Function factories
function multiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = multiplier(2);
const triple = multiplier(3);

double(5); // 10
triple(5); // 15

// 3. Event handlers with private state
function setupButton(buttonId) {
  let clickCount = 0;
  
  document.getElementById(buttonId).addEventListener('click', function() {
    clickCount++;
    console.log(`Clicked ${clickCount} times`);
  });
}

// 4. Memoization
function memoize(fn) {
  const cache = {}; // Closure variable
  
  return function(...args) {
    const key = JSON.stringify(args);
    if (key in cache) {
      console.log('From cache');
      return cache[key];
    }
    const result = fn(...args);
    cache[key] = result;
    return result;
  };
}

const expensiveCalc = memoize((n) => {
  console.log('Calculating...');
  return n * n;
});

expensiveCalc(5); // Calculating... 25
expensiveCalc(5); // From cache 25
```

### Q37: What is currying?

**Answer:**
Currying transforms a function with multiple arguments into a sequence of functions each taking a single argument.

```javascript
// Regular function
function add(a, b, c) {
  return a + b + c;
}
add(1, 2, 3); // 6

// Curried version
function curriedAdd(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}

curriedAdd(1)(2)(3); // 6

// Or with arrow functions
const curriedAdd = a => b => c => a + b + c;

// Practical use cases

// 1. Partial application
const add5 = curriedAdd(5);
const add5And3 = add5(3);
console.log(add5And3(10)); // 18

// 2. Function composition
const greet = greeting => name => `${greeting}, ${name}!`;

const sayHello = greet('Hello');
const sayHi = greet('Hi');

sayHello('Alice'); // "Hello, Alice!"
sayHi('Bob');      // "Hi, Bob!"

// 3. Event handlers
const handleEvent = eventType => selector => callback => {
  document.querySelector(selector)
    .addEventListener(eventType, callback);
};

const onClick = handleEvent('click');
const onButtonClick = onClick('button');

onButtonClick(() => console.log('Clicked!'));

// Generic curry function
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    return function(...nextArgs) {
      return curried.apply(this, [...args, ...nextArgs]);
    };
  };
}

// Usage
function sum(a, b, c) {
  return a + b + c;
}

const curriedSum = curry(sum);
curriedSum(1)(2)(3);     // 6
curriedSum(1, 2)(3);     // 6
curriedSum(1)(2, 3);     // 6
```

### Q38: What is debounce vs throttle?

**Answer:**

**Debounce - Execute after pause:**
```javascript
// Debounce: Only execute after user stops typing
function debounce(func, delay) {
  let timeoutId;
  
  return function(...args) {
    clearTimeout(timeoutId);
    
    timeoutId = setTimeout(() => {
      func.apply(this, args);
    }, delay);
  };
}

// Usage: Search as user types
const searchInput = document.querySelector('#search');

const search = debounce((query) => {
  console.log('Searching for:', query);
  // API call here
}, 300);

searchInput.addEventListener('input', (e) => {
  search(e.target.value);
});

// User types: "h" "e" "l" "l" "o"
// Only searches once, 300ms after they stop
```

**Throttle - Execute at intervals:**
```javascript
// Throttle: Execute at most once per interval
function throttle(func, limit) {
  let inThrottle;
  
  return function(...args) {
    if (!inThrottle) {
      func.apply(this, args);
      inThrottle = true;
      
      setTimeout(() => {
        inThrottle = false;
      }, limit);
    }
  };
}

// Usage: Scroll event
const handleScroll = throttle(() => {
  console.log('Scroll position:', window.scrollY);
}, 1000);

window.addEventListener('scroll', handleScroll);

// User scrolls continuously
// Logs position once per second
```

**Comparison:**

| Feature | Debounce | Throttle |
|---------|----------|----------|
| Execution | After pause | At intervals |
| Use case | Search input | Scroll, resize |
| Frequency | Once after delay | Every N ms |
| Example | Type to search | Infinite scroll |

**When to use:**
- **Debounce**: Search autocomplete, form validation, save draft
- **Throttle**: Scroll events, resize events, mouse movement tracking

---

## Advanced JavaScript

### Q39: What is prototypal inheritance?

**Answer:**
Every JavaScript object has a hidden `[[Prototype]]` property that references another object. This chain enables inheritance.

```javascript
// Constructor function
function Person(name) {
  this.name = name;
}

// Add method to prototype
Person.prototype.greet = function() {
  return `Hello, I'm ${this.name}`;
};

const john = new Person('John');
console.log(john.greet()); // "Hello, I'm John"

// Inheritance chain
console.log(john.__proto__ === Person.prototype); // true
console.log(Person.prototype.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__ === null); // true (end of chain)

// Inheritance
function Employee(name, job) {
  Person.call(this, name); // Call parent constructor
  this.job = job;
}

// Set up prototype chain
Employee.prototype = Object.create(Person.prototype);
Employee.prototype.constructor = Employee;

Employee.prototype.work = function() {
  return `${this.name} is working as ${this.job}`;
};

const jane = new Employee('Jane', 'Developer');
console.log(jane.greet()); // "Hello, I'm Jane" (inherited)
console.log(jane.work());  // "Jane is working as Developer"

// ES6 Class syntax (same prototype chain under the hood)
class PersonClass {
  constructor(name) {
    this.name = name;
  }
  
  greet() {
    return `Hello, I'm ${this.name}`;
  }
}

class EmployeeClass extends PersonClass {
  constructor(name, job) {
    super(name);
    this.job = job;
  }
  
  work() {
    return `${this.name} is working as ${this.job}`;
  }
}
```

### Q40: What is bind(), call(), and apply()?

**Answer:**
These methods control what `this` refers to when calling a function.

**call() - Invoke immediately with arguments:**
```javascript
function greet(greeting, punctuation) {
  return `${greeting}, ${this.name}${punctuation}`;
}

const person = { name: 'Alice' };

greet.call(person, 'Hello', '!'); // "Hello, Alice!"
```

**apply() - Invoke immediately with array:**
```javascript
greet.apply(person, ['Hi', '!!']); // "Hi, Alice!!"

// Useful for Math functions
const numbers = [5, 6, 2, 3, 7];
Math.max.apply(null, numbers); // 7

// Modern alternative: spread
Math.max(...numbers); // 7
```

**bind() - Return new function:**
```javascript
const greetAlice = greet.bind(person);
greetAlice('Hey', '?'); // "Hey, Alice?"

// Partial application
const sayHello = greet.bind(person, 'Hello');
sayHello('!!!'); // "Hello, Alice!!!"

// Event handlers
class Button {
  constructor(label) {
    this.label = label;
    this.clicks = 0;
  }
  
  handleClick() {
    this.clicks++;
    console.log(`${this.label}: ${this.clicks} clicks`);
  }
}

const button = new Button('Submit');

// Without bind - this is the DOM element
element.addEventListener('click', button.handleClick); // ❌ Error

// With bind - this is the button object
element.addEventListener('click', button.handleClick.bind(button)); // ✅

// Or use arrow function
element.addEventListener('click', () => button.handleClick()); // ✅
```

**Comparison:**

| Method | Invokes | Arguments | Returns |
|--------|---------|-----------|---------|
| call | Immediately | Individual | Result |
| apply | Immediately | Array | Result |
| bind | Later | Partial OK | New function |

### Q41: Write a polyfill for bind()

**Answer:**
```javascript
// Custom bind implementation
Function.prototype.myBind = function(context, ...args) {
  // Store the original function
  const fn = this;
  
  // Return a new function
  return function(...newArgs) {
    // Call original function with correct context
    // Combine initial args + new args
    return fn.apply(context, [...args, ...newArgs]);
  };
};

// Test
function greet(greeting, punctuation) {
  return `${greeting}, ${this.name}${punctuation}`;
}

const person = { name: 'Alice' };

const bound = greet.myBind(person, 'Hello');
console.log(bound('!')); // "Hello, Alice!"
```

### Q42: What is map(), filter(), and reduce()?

**Answer:**

**map() - Transform each element:**
```javascript
const numbers = [1, 2, 3, 4, 5];

const doubled = numbers.map(n => n * 2);
// [2, 4, 6, 8, 10]

const names = ['alice', 'bob', 'charlie'];
const capitalized = names.map(name => 
  name.charAt(0).toUpperCase() + name.slice(1)
);
// ['Alice', 'Bob', 'Charlie']

// Extract property
const users = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' }
];
const userNames = users.map(user => user.name);
// ['John', 'Jane']
```

**filter() - Keep elements that pass test:**
```javascript
const numbers = [1, 2, 3, 4, 5, 6];

const evens = numbers.filter(n => n % 2 === 0);
// [2, 4, 6]

const adults = users.filter(user => user.age >= 18);

// Remove falsy values
const values = [0, 1, false, 2, '', 3, null, undefined, 4];
const truthy = values.filter(Boolean);
// [1, 2, 3, 4]
```

**reduce() - Reduce to single value:**
```javascript
const numbers = [1, 2, 3, 4, 5];

// Sum
const sum = numbers.reduce((acc, num) => acc + num, 0);
// 15

// Count occurrences
const fruits = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple'];
const count = fruits.reduce((acc, fruit) => {
  acc[fruit] = (acc[fruit] || 0) + 1;
  return acc;
}, {});
// { apple: 3, banana: 2, orange: 1 }

// Group by property
const people = [
  { name: 'Alice', age: 25 },
  { name: 'Bob', age: 30 },
  { name: 'Charlie', age: 25 }
];

const byAge = people.reduce((acc, person) => {
  const age = person.age;
  if (!acc[age]) {
    acc[age] = [];
  }
  acc[age].push(person);
  return acc;
}, {});
// { 25: [{Alice}, {Charlie}], 30: [{Bob}] }

// Flatten array
const nested = [[1, 2], [3, 4], [5, 6]];
const flattened = nested.reduce((acc, arr) => acc.concat(arr), []);
// [1, 2, 3, 4, 5, 6]

// Or use flat()
nested.flat(); // [1, 2, 3, 4, 5, 6]
```

**Chaining:**
```javascript
const users = [
  { name: 'Alice', age: 25, active: true },
  { name: 'Bob', age: 30, active: false },
  { name: 'Charlie', age: 35, active: true }
];

const result = users
  .filter(user => user.active)
  .map(user => user.name)
  .join(', ');

console.log(result); // "Alice, Charlie"
```

### Q43: What is the spread operator and rest operator?

**Answer:**

**Spread (...) - Expand elements:**
```javascript
// Arrays
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

const combined = [...arr1, ...arr2];
// [1, 2, 3, 4, 5, 6]

const copy = [...arr1]; // Shallow copy

// Objects
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };

const merged = { ...obj1, ...obj2 };
// { a: 1, b: 2, c: 3, d: 4 }

// Override properties
const updated = { ...obj1, b: 10 };
// { a: 1, b: 10 }

// Function arguments
const numbers = [1, 2, 3];
Math.max(...numbers); // 3

// Add elements
const newArr = [...arr1, 4, 5, ...arr2];
```

**Rest (...) - Collect elements:**
```javascript
// Function parameters
function sum(...numbers) {
  return numbers.reduce((acc, n) => acc + n, 0);
}

sum(1, 2, 3, 4, 5); // 15

// With other parameters
function greet(greeting, ...names) {
  return `${greeting} ${names.join(', ')}!`;
}

greet('Hello', 'Alice', 'Bob', 'Charlie');
// "Hello Alice, Bob, Charlie!"

// Array destructuring
const [first, second, ...rest] = [1, 2, 3, 4, 5];
// first = 1, second = 2, rest = [3, 4, 5]

// Object destructuring
const { name, age, ...otherProps } = {
  name: 'John',
  age: 30,
  city: 'NYC',
  job: 'Developer'
};
// name = 'John', age = 30
// otherProps = { city: 'NYC', job: 'Developer' }
```

**Key difference:**
- **Spread**: Expands into individual elements
- **Rest**: Collects into array

### Q44: What is destructuring?

**Answer:**
Destructuring unpacks values from arrays or properties from objects.

**Array destructuring:**
```javascript
// Basic
const [a, b] = [1, 2];
// a = 1, b = 2

// Skip elements
const [first, , third] = [1, 2, 3];
// first = 1, third = 3

// Rest
const [head, ...tail] = [1, 2, 3, 4];
// head = 1, tail = [2, 3, 4]

// Default values
const [x = 5, y = 10] = [1];
// x = 1, y = 10

// Swap variables
let m = 1, n = 2;
[m, n] = [n, m];
// m = 2, n = 1
```

**Object destructuring:**
```javascript
// Basic
const { name, age } = { name: 'John', age: 30 };

// Rename
const { name: userName, age: userAge } = user;

// Default values
const { name = 'Guest', age = 0 } = {};

// Nested
const user = {
  name: 'Alice',
  address: {
    city: 'NYC',
    zip: '10001'
  }
};

const { address: { city } } = user;
// city = 'NYC'

// Function parameters
function greet({ name, age }) {
  console.log(`${name} is ${age} years old`);
}

greet({ name: 'Bob', age: 25 });

// With defaults
function greet({ name = 'Guest', age = 0 } = {}) {
  console.log(`${name} is ${age} years old`);
}

greet(); // "Guest is 0 years old"
```

### Q45: What are symbols and their use cases?

**Answer:**
Symbol is a primitive type for creating unique identifiers.

```javascript
// Create symbols
const sym1 = Symbol();
const sym2 = Symbol('description');
const sym3 = Symbol('description');

console.log(sym2 === sym3); // false (always unique!)

// Use cases

// 1. Unique object properties
const ID = Symbol('id');
const user = {
  name: 'John',
  [ID]: 12345 // Hidden from normal enumeration
};

console.log(user[ID]); // 12345
console.log(Object.keys(user)); // ['name'] (Symbol not included)

// 2. Private properties
const _private = Symbol('private');

class MyClass {
  constructor() {
    this[_private] = 'secret data';
  }
  
  getPrivate() {
    return this[_private];
  }
}

const obj = new MyClass();
console.log(obj.getPrivate()); // 'secret data'
console.log(obj[_private]);    // undefined (different symbol)

// 3. Well-known symbols
// Symbol.iterator
const iterableObj = {
  data: [1, 2, 3],
  [Symbol.iterator]() {
    let index = 0;
    return {
      next: () => ({
        value: this.data[index++],
        done: index > this.data.length
      })
    };
  }
};

for (let value of iterableObj) {
  console.log(value); // 1, 2, 3
}

// Symbol.toStringTag
class MyArray {
  get [Symbol.toStringTag]() {
    return 'MyArray';
  }
}

console.log(Object.prototype.toString.call(new MyArray()));
// "[object MyArray]"

// Global symbol registry
const globalSym1 = Symbol.for('app.id');
const globalSym2 = Symbol.for('app.id');
console.log(globalSym1 === globalSym2); // true (same global symbol)
```

### Q46: What is a WeakMap vs Map?

**Answer:**

**Map - Any key type, enumerable:**
```javascript
const map = new Map();

// Any type as key
const objKey = { id: 1 };
const funcKey = () => {};

map.set('string', 'value');
map.set(123, 'number key');
map.set(objKey, 'object key');
map.set(funcKey, 'function key');

map.get('string');  // 'value'
map.has(123);       // true
map.delete(123);    // true
map.size;           // 3

// Iteration
for (let [key, value] of map) {
  console.log(key, value);
}

map.forEach((value, key) => {
  console.log(key, value);
});
```

**WeakMap - Only object keys, not enumerable:**
```javascript
const weakMap = new WeakMap();

const obj1 = { id: 1 };
const obj2 = { id: 2 };

weakMap.set(obj1, 'data1');
weakMap.set(obj2, 'data2');

// weakMap.set('string', 'value'); // TypeError! (must be object)

weakMap.get(obj1); // 'data1'
weakMap.has(obj2); // true
weakMap.delete(obj2); // true

// No iteration (not enumerable)
// weakMap.forEach(...) // Error!
// weakMap.size // undefined

// Garbage collection
let obj = { id: 3 };
weakMap.set(obj, 'data');
obj = null; // WeakMap entry is garbage collected!
```

**Comparison:**

| Feature | Map | WeakMap |
|---------|-----|---------|
| Keys | Any type | Objects only |
| Enumerable | Yes | No |
| Size | Available | Not available |
| Garbage Collection | Manual | Automatic |

**Use cases:**

**Map:**
- Need to iterate
- Any key type
- Ordered insertion

**WeakMap:**
- Store metadata about objects
- Cache/memoization
- Private data
- Prevent memory leaks

```javascript
// WeakMap use case: Private data
const privateData = new WeakMap();

class Person {
  constructor(name, ssn) {
    this.name = name;
    privateData.set(this, { ssn }); // ssn is private
  }
  
  getSSN() {
    return privateData.get(this).ssn;
  }
}

const person = new Person('John', '123-45-6789');
console.log(person.name);     // 'John'
console.log(person.ssn);      // undefined (private)
console.log(person.getSSN()); // '123-45-6789'
```

### Q47: What is a generator function?

**Answer:**
Generator functions can pause execution and resume later, yielding multiple values.

```javascript
// Generator function (function*)
function* numberGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = numberGenerator();

console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: 3, done: false }
console.log(gen.next()); // { value: undefined, done: true }

// Iterate with for...of
for (let num of numberGenerator()) {
  console.log(num); // 1, 2, 3
}

// Infinite generator
function* infiniteNumbers() {
  let i = 1;
  while (true) {
    yield i++;
  }
}

const infinite = infiniteNumbers();
console.log(infinite.next().value); // 1
console.log(infinite.next().value); // 2

// Generator with parameters
function* echo() {
  while (true) {
    const input = yield; // Receive value
    console.log(`Echo: ${input}`);
  }
}

const echoGen = echo();
echoGen.next();           // Start generator
echoGen.next('Hello');    // Echo: Hello
echoGen.next('World');    // Echo: World

// Real-world example: ID generator
function* idGenerator() {
  let id = 1;
  while (true) {
    yield `ID-${id++}`;
  }
}

const ids = idGenerator();
console.log(ids.next().value); // 'ID-1'
console.log(ids.next().value); // 'ID-2'

// Async generator
async function* asyncGenerator() {
  yield await Promise.resolve(1);
  yield await Promise.resolve(2);
  yield await Promise.resolve(3);
}

(async () => {
  for await (let num of asyncGenerator()) {
    console.log(num); // 1, 2, 3
  }
})();
```

---

## Lead/Architect Level

### Q48: How does the V8 JavaScript engine work?

**Answer:**

**V8 Architecture:**
```
Source Code
    ↓
Parser → AST (Abstract Syntax Tree)
    ↓
Ignition (Interpreter) → Bytecode
    ↓
TurboFan (JIT Compiler) → Optimized Machine Code
```

**Execution phases:**

**1. Parsing:**
- Source code → Tokens (lexical analysis)
- Tokens → AST (syntax analysis)

**2. Ignition (Interpreter):**
- AST → Bytecode
- Executes bytecode
- Collects profiling data

**3. TurboFan (Optimizing Compiler):**
- Identifies "hot" code (frequently executed)
- Compiles bytecode → optimized machine code
- Aggressive optimizations
- Deoptimization if assumptions fail

**Memory Management:**
```
Heap:
- New Space (young generation)
- Old Space (old generation)
- Large Object Space
- Code Space

Garbage Collection:
- Scavenger (New Space): Fast, frequent
- Mark-Sweep-Compact (Old Space): Slower, less frequent
- Incremental marking
- Concurrent sweeping
```

**Hidden Classes (Shapes):**
```javascript
// V8 creates hidden classes for objects with same structure
class Point {
  constructor(x, y) {
    this.x = x; // Hidden class C0 → C1
    this.y = y; // Hidden class C1 → C2
  }
}

const p1 = new Point(1, 2); // Uses hidden class C2
const p2 = new Point(3, 4); // Reuses hidden class C2 (fast!)

// Breaks optimization
const p3 = { x: 1, y: 2 };
p3.z = 3; // Different hidden class (slow)
```

**Inline Caching:**
```javascript
// V8 caches property lookups
function getX(point) {
  return point.x;
}

// First call: Lookup + cache
getX({ x: 1, y: 2 });

// Subsequent calls: Use cache (fast!)
getX({ x: 3, y: 4 });
getX({ x: 5, y: 6 });
```

### Q49: Performance optimization techniques

**Answer:**

**1. Minimize DOM manipulation:**
```javascript
// ❌ Bad: Multiple reflows
for (let i = 0; i < 100; i++) {
  document.body.innerHTML += `<div>${i}</div>`;
}

// ✅ Good: Single reflow
const html = [];
for (let i = 0; i < 100; i++) {
  html.push(`<div>${i}</div>`);
}
document.body.innerHTML = html.join('');

// ✅ Better: Document fragment
const fragment = document.createDocumentFragment();
for (let i = 0; i < 100; i++) {
  const div = document.createElement('div');
  div.textContent = i;
  fragment.appendChild(div);
}
document.body.appendChild(fragment);
```

**2. Debounce/throttle expensive operations:**
```javascript
// Search as user types
const debouncedSearch = debounce(searchAPI, 300);
input.addEventListener('input', e => debouncedSearch(e.target.value));

// Scroll handler
const throttledScroll = throttle(handleScroll, 100);
window.addEventListener('scroll', throttledScroll);
```

**3. Use efficient data structures:**
```javascript
// ❌ Array search: O(n)
const users = [{ id: 1 }, { id: 2 }, ...];
const user = users.find(u => u.id === 100);

// ✅ Map lookup: O(1)
const usersMap = new Map(users.map(u => [u.id, u]));
const user = usersMap.get(100);

// ✅ Set for uniqueness: O(1)
const uniqueIds = new Set(users.map(u => u.id));
```

**4. Lazy loading and code splitting:**
```javascript
// Dynamic import
const module = await import('./heavy-module.js');

// React lazy
const HeavyComponent = React.lazy(() => import('./HeavyComponent'));
```

**5. Memoization:**
```javascript
const memoize = (fn) => {
  const cache = new Map();
  return (...args) => {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
};

const fibonacci = memoize((n) => {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
});
```

**6. Avoid memory leaks:**
```javascript
// ❌ Event listener leak
element.addEventListener('click', handleClick);

// ✅ Remove when done
element.removeEventListener('click', handleClick);

// ❌ Timer leak
setInterval(doSomething, 1000);

// ✅ Clear when done
const intervalId = setInterval(doSomething, 1000);
clearInterval(intervalId);

// ❌ Closure leak
function createHuge() {
  const hugeArray = new Array(1000000);
  return function() {
    console.log(hugeArray.length); // hugeArray never garbage collected
  };
}

// ✅ Don't capture unnecessarily
function createHuge() {
  const hugeArray = new Array(1000000);
  const length = hugeArray.length;
  return function() {
    console.log(length); // hugeArray can be garbage collected
  };
}
```

**7. Use Web Workers for heavy computation:**
```javascript
// main.js
const worker = new Worker('worker.js');

worker.postMessage({ data: largeDataSet });

worker.onmessage = (e) => {
  console.log('Result:', e.data);
};

// worker.js
self.onmessage = (e) => {
  const result = processLargeData(e.data.data);
  self.postMessage(result);
};
```

### Q50: Security best practices

**Answer:**

**1. Prevent XSS (Cross-Site Scripting):**
```javascript
// ❌ Dangerous: innerHTML with user input
const userName = getUserInput();
div.innerHTML = `<p>Hello ${userName}</p>`; // XSS!

// ✅ Safe: textContent or sanitize
div.textContent = `Hello ${userName}`;

// ✅ Or use library like DOMPurify
import DOMPurify from 'dompurify';
div.innerHTML = DOMPurify.sanitize(userInput);

// ✅ CSP (Content Security Policy)
// Set in HTTP headers or meta tag
<meta http-equiv="Content-Security-Policy" 
      content="default-src 'self'; script-src 'self' https://trusted.cdn.com">
```

**2. Prevent CSRF (Cross-Site Request Forgery):**
```javascript
// Use CSRF tokens
fetch('/api/transfer', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-CSRF-Token': csrfToken
  },
  body: JSON.stringify({ amount: 100 })
});

// SameSite cookies
document.cookie = "session=abc123; SameSite=Strict; Secure; HttpOnly";
```

**3. Secure authentication:**
```javascript
// ❌ Don't store sensitive data in localStorage
localStorage.setItem('password', password); // Never!

// ✅ Use httpOnly cookies for tokens
// Set-Cookie: token=xyz; HttpOnly; Secure; SameSite=Strict

// ✅ JWT best practices
const token = jwt.sign(
  { userId: user.id },
  process.env.JWT_SECRET,
  { expiresIn: '15m' }
);

// ✅ Refresh token flow
const accessToken = generateAccessToken(); // Short lived (15 min)
const refreshToken = generateRefreshToken(); // Long lived (7 days)
```

**4. Input validation:**
```javascript
// Always validate on server AND client
function validateEmail(email) {
  const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return re.test(email);
}

// Sanitize SQL queries (use parameterized queries)
// ❌ Bad
const query = `SELECT * FROM users WHERE id = ${userId}`;

// ✅ Good
const query = 'SELECT * FROM users WHERE id = ?';
db.query(query, [userId]);
```

**5. Secure data transmission:**
```javascript
// Always use HTTPS
// Force HTTPS redirect
if (location.protocol !== 'https:') {
  location.replace(`https:${location.href.substring(location.protocol.length)}`);
}

// Encrypt sensitive data
import crypto from 'crypto';

function encrypt(text, key) {
  const cipher = crypto.createCipher('aes-256-cbc', key);
  let encrypted = cipher.update(text, 'utf8', 'hex');
  encrypted += cipher.final('hex');
  return encrypted;
}
```

This comprehensive JavaScript guide covers 50 essential questions from basics to architect-level concepts. Each answer includes detailed explanations and practical code examples to help you master JavaScript interviews!

---

## Design Principles

### Q51: What are SOLID Principles in JavaScript?

**Answer:**

SOLID is a set of five design principles that make software more maintainable, flexible, and scalable. While originally for OOP, they apply to JavaScript too.

**The Five Principles:**
1. **S**ingle Responsibility Principle
2. **O**pen/Closed Principle
3. **L**iskov Substitution Principle
4. **I**nterface Segregation Principle
5. **D**ependency Inversion Principle

---

### Q51.1: Single Responsibility Principle (SRP)

**Definition:** A class/module should have only one reason to change. Each class should have a single responsibility.

**❌ Bad Example - Multiple Responsibilities:**

```javascript
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }
  
  // User data management
  getUserInfo() {
    return `${this.name} (${this.email})`;
  }
  
  // Database operations (different responsibility!)
  save() {
    const db = new Database();
    db.insert('users', {
      name: this.name,
      email: this.email
    });
  }
  
  // Email operations (another different responsibility!)
  sendEmail(subject, message) {
    const emailService = new EmailService();
    emailService.send(this.email, subject, message);
  }
  
  // Logging (yet another responsibility!)
  log() {
    console.log(`User action: ${this.name}`);
  }
}

// Problems:
// - If DB changes, User class must change
// - If email service changes, User class must change
// - If logging format changes, User class must change
// - Hard to test individual responsibilities
```

**✅ Good Example - Single Responsibility:**

```javascript
// 1. User class - Only manages user data
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }
  
  getUserInfo() {
    return `${this.name} (${this.email})`;
  }
  
  validate() {
    if (!this.name || !this.email) {
      throw new Error('Invalid user data');
    }
    return true;
  }
}

// 2. UserRepository - Only handles database operations
class UserRepository {
  constructor(database) {
    this.db = database;
  }
  
  save(user) {
    return this.db.insert('users', {
      name: user.name,
      email: user.email
    });
  }
  
  findById(id) {
    return this.db.findOne('users', { id });
  }
  
  delete(id) {
    return this.db.remove('users', { id });
  }
}

// 3. EmailService - Only handles email sending
class EmailService {
  constructor(emailProvider) {
    this.provider = emailProvider;
  }
  
  sendWelcomeEmail(user) {
    return this.provider.send({
      to: user.email,
      subject: 'Welcome!',
      body: `Hello ${user.name}, welcome to our platform!`
    });
  }
  
  sendPasswordReset(user, resetLink) {
    return this.provider.send({
      to: user.email,
      subject: 'Password Reset',
      body: `Click here to reset: ${resetLink}`
    });
  }
}

// 4. Logger - Only handles logging
class Logger {
  log(level, message, metadata = {}) {
    const timestamp = new Date().toISOString();
    console.log(`[${timestamp}] [${level}] ${message}`, metadata);
  }
  
  info(message, metadata) {
    this.log('INFO', message, metadata);
  }
  
  error(message, metadata) {
    this.log('ERROR', message, metadata);
  }
}

// Usage - Each class has a single, well-defined responsibility
const user = new User('John Doe', 'john@example.com');
const userRepo = new UserRepository(database);
const emailService = new EmailService(emailProvider);
const logger = new Logger();

user.validate();
userRepo.save(user);
emailService.sendWelcomeEmail(user);
logger.info('User registered', { userId: user.id });
```

**Benefits:**
- ✅ Easier to understand and maintain
- ✅ Each class can be tested independently
- ✅ Changes in one area don't affect others
- ✅ Easier to reuse components

---

### Q51.2: Open/Closed Principle (OCP)

**Definition:** Software entities should be open for extension but closed for modification. Add new functionality without changing existing code.

**❌ Bad Example - Modification Required:**

```javascript
class PaymentProcessor {
  processPayment(amount, type) {
    if (type === 'credit-card') {
      console.log(`Processing credit card payment: $${amount}`);
      // Credit card logic
    } else if (type === 'paypal') {
      console.log(`Processing PayPal payment: $${amount}`);
      // PayPal logic
    } else if (type === 'bitcoin') {
      console.log(`Processing Bitcoin payment: $${amount}`);
      // Bitcoin logic
    }
    // Adding new payment method requires modifying this class!
  }
}

// Problem: To add a new payment method (e.g., Stripe),
// we must modify the PaymentProcessor class
```

**✅ Good Example - Extension Without Modification:**

```javascript
// Base interface/class
class PaymentMethod {
  process(amount) {
    throw new Error('process() must be implemented');
  }
}

// Concrete implementations
class CreditCardPayment extends PaymentMethod {
  constructor(cardNumber, cvv) {
    super();
    this.cardNumber = cardNumber;
    this.cvv = cvv;
  }
  
  process(amount) {
    console.log(`Processing credit card payment: $${amount}`);
    // Credit card specific logic
    return {
      success: true,
      transactionId: 'CC-' + Date.now(),
      method: 'Credit Card'
    };
  }
}

class PayPalPayment extends PaymentMethod {
  constructor(email) {
    super();
    this.email = email;
  }
  
  process(amount) {
    console.log(`Processing PayPal payment: $${amount}`);
    // PayPal specific logic
    return {
      success: true,
      transactionId: 'PP-' + Date.now(),
      method: 'PayPal'
    };
  }
}

class BitcoinPayment extends PaymentMethod {
  constructor(walletAddress) {
    super();
    this.walletAddress = walletAddress;
  }
  
  process(amount) {
    console.log(`Processing Bitcoin payment: $${amount}`);
    // Bitcoin specific logic
    return {
      success: true,
      transactionId: 'BTC-' + Date.now(),
      method: 'Bitcoin'
    };
  }
}

// Adding new payment method WITHOUT modifying existing code
class StripePayment extends PaymentMethod {
  constructor(token) {
    super();
    this.token = token;
  }
  
  process(amount) {
    console.log(`Processing Stripe payment: $${amount}`);
    // Stripe specific logic
    return {
      success: true,
      transactionId: 'STRIPE-' + Date.now(),
      method: 'Stripe'
    };
  }
}

// Payment processor - never needs modification
class PaymentProcessor {
  processPayment(paymentMethod, amount) {
    return paymentMethod.process(amount);
  }
}

// Usage
const processor = new PaymentProcessor();

const creditCard = new CreditCardPayment('1234-5678', '123');
processor.processPayment(creditCard, 100);

const paypal = new PayPalPayment('user@example.com');
processor.processPayment(paypal, 200);

const stripe = new StripePayment('tok_123456');
processor.processPayment(stripe, 300);
```

**Another Example - Discount System:**

```javascript
// ❌ Bad - Requires modification
class PriceCalculator {
  calculateDiscount(price, customerType) {
    if (customerType === 'regular') {
      return price * 0.05; // 5% discount
    } else if (customerType === 'premium') {
      return price * 0.10; // 10% discount
    } else if (customerType === 'vip') {
      return price * 0.20; // 20% discount
    }
  }
}

// ✅ Good - Open for extension
class DiscountStrategy {
  calculate(price) {
    throw new Error('calculate() must be implemented');
  }
}

class RegularDiscount extends DiscountStrategy {
  calculate(price) {
    return price * 0.05; // 5% discount
  }
}

class PremiumDiscount extends DiscountStrategy {
  calculate(price) {
    return price * 0.10; // 10% discount
  }
}

class VIPDiscount extends DiscountStrategy {
  calculate(price) {
    return price * 0.20; // 20% discount
  }
}

// Add new discount type without modifying existing code
class BlackFridayDiscount extends DiscountStrategy {
  calculate(price) {
    return price * 0.50; // 50% discount
  }
}

class PriceCalculator {
  constructor(discountStrategy) {
    this.discountStrategy = discountStrategy;
  }
  
  setDiscountStrategy(strategy) {
    this.discountStrategy = strategy;
  }
  
  calculateFinalPrice(price) {
    const discount = this.discountStrategy.calculate(price);
    return price - discount;
  }
}

// Usage
const calculator = new PriceCalculator(new RegularDiscount());
console.log(calculator.calculateFinalPrice(100)); // 95

calculator.setDiscountStrategy(new VIPDiscount());
console.log(calculator.calculateFinalPrice(100)); // 80

calculator.setDiscountStrategy(new BlackFridayDiscount());
console.log(calculator.calculateFinalPrice(100)); // 50
```

---

### Q51.3: Liskov Substitution Principle (LSP)

**Definition:** Objects of a superclass should be replaceable with objects of a subclass without breaking the application. Derived classes must be substitutable for their base classes.

**❌ Bad Example - Violates LSP:**

```javascript
class Bird {
  fly() {
    console.log('Flying...');
  }
}

class Sparrow extends Bird {
  fly() {
    console.log('Sparrow flying...');
  }
}

class Penguin extends Bird {
  fly() {
    // Penguins can't fly!
    throw new Error('Penguins cannot fly!');
  }
}

function makeBirdFly(bird) {
  bird.fly(); // Expects all birds to fly
}

const sparrow = new Sparrow();
makeBirdFly(sparrow); // ✅ Works

const penguin = new Penguin();
makeBirdFly(penguin); // ❌ Throws error! Violates LSP
```

**✅ Good Example - Follows LSP:**

```javascript
// Base class with common behavior
class Bird {
  constructor(name) {
    this.name = name;
  }
  
  eat() {
    console.log(`${this.name} is eating`);
  }
  
  makeSound() {
    console.log(`${this.name} makes a sound`);
  }
}

// Separate interface for flying birds
class FlyingBird extends Bird {
  fly() {
    console.log(`${this.name} is flying`);
  }
}

// Separate interface for swimming birds
class SwimmingBird extends Bird {
  swim() {
    console.log(`${this.name} is swimming`);
  }
}

class Sparrow extends FlyingBird {
  makeSound() {
    console.log('Sparrow: Chirp chirp!');
  }
}

class Eagle extends FlyingBird {
  makeSound() {
    console.log('Eagle: Screech!');
  }
}

class Penguin extends SwimmingBird {
  makeSound() {
    console.log('Penguin: Honk honk!');
  }
}

// Functions that work with appropriate types
function makeFlyingBirdFly(bird) {
  if (bird instanceof FlyingBird) {
    bird.fly();
  }
}

function makeBirdEat(bird) {
  bird.eat(); // All birds can eat
}

// Usage
const sparrow = new Sparrow('Sparrow');
const penguin = new Penguin('Penguin');

makeBirdEat(sparrow);  // ✅ Works
makeBirdEat(penguin);  // ✅ Works

makeFlyingBirdFly(sparrow);  // ✅ Works
// makeFlyingBirdFly(penguin); // Won't compile/will be caught by TypeScript
```

**Another Example - Rectangle/Square Problem:**

```javascript
// ❌ Bad - Square violates LSP
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }
  
  setWidth(width) {
    this.width = width;
  }
  
  setHeight(height) {
    this.height = height;
  }
  
  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width; // Square must have equal sides
  }
  
  setHeight(height) {
    this.width = height; // Square must have equal sides
    this.height = height;
  }
}

function testRectangle(rectangle) {
  rectangle.setWidth(5);
  rectangle.setHeight(4);
  console.log(`Expected: 20, Got: ${rectangle.getArea()}`);
  // Works for Rectangle (20), fails for Square (16)
}

// ✅ Good - Separate classes
class Shape {
  getArea() {
    throw new Error('getArea() must be implemented');
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }
  
  setWidth(width) {
    this.width = width;
  }
  
  setHeight(height) {
    this.height = height;
  }
  
  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(side) {
    super();
    this.side = side;
  }
  
  setSide(side) {
    this.side = side;
  }
  
  getArea() {
    return this.side * this.side;
  }
}

// Now each can be used correctly
const rect = new Rectangle(5, 4);
console.log(rect.getArea()); // 20

const square = new Square(5);
console.log(square.getArea()); // 25
```

---

### Q51.4: Interface Segregation Principle (ISP)

**Definition:** Clients should not be forced to depend on interfaces they don't use. Create specific interfaces instead of one general-purpose interface.

**❌ Bad Example - Fat Interface:**

```javascript
class Worker {
  work() {
    throw new Error('Must implement work()');
  }
  
  eat() {
    throw new Error('Must implement eat()');
  }
  
  sleep() {
    throw new Error('Must implement sleep()');
  }
}

class HumanWorker extends Worker {
  work() {
    console.log('Human working...');
  }
  
  eat() {
    console.log('Human eating...');
  }
  
  sleep() {
    console.log('Human sleeping...');
  }
}

class RobotWorker extends Worker {
  work() {
    console.log('Robot working...');
  }
  
  eat() {
    // Robots don't eat!
    throw new Error('Robots do not eat');
  }
  
  sleep() {
    // Robots don't sleep!
    throw new Error('Robots do not sleep');
  }
}

// Problem: Robot is forced to implement methods it doesn't need
```

**✅ Good Example - Segregated Interfaces:**

```javascript
// Separate interfaces for different capabilities
class Workable {
  work() {
    throw new Error('Must implement work()');
  }
}

class Eatable {
  eat() {
    throw new Error('Must implement eat()');
  }
}

class Sleepable {
  sleep() {
    throw new Error('Must implement sleep()');
  }
}

// Human implements all three
class HumanWorker extends Workable {
  work() {
    console.log('Human working...');
  }
}

class HumanEater extends Eatable {
  eat() {
    console.log('Human eating...');
  }
}

class HumanSleeper extends Sleepable {
  sleep() {
    console.log('Human sleeping...');
  }
}

// Composition for Human
class Human {
  constructor() {
    this.worker = new HumanWorker();
    this.eater = new HumanEater();
    this.sleeper = new HumanSleeper();
  }
  
  work() {
    this.worker.work();
  }
  
  eat() {
    this.eater.eat();
  }
  
  sleep() {
    this.sleeper.sleep();
  }
}

// Robot only implements what it needs
class RobotWorker extends Workable {
  work() {
    console.log('Robot working...');
  }
}

class Robot {
  constructor() {
    this.worker = new RobotWorker();
  }
  
  work() {
    this.worker.work();
  }
  
  // No eat() or sleep() methods - robots don't need them!
}

// Usage
const human = new Human();
human.work();
human.eat();
human.sleep();

const robot = new Robot();
robot.work();
// robot.eat();  // Not available - robots don't eat!
// robot.sleep(); // Not available - robots don't sleep!
```

**Another Example - Multi-Function Printer:**

```javascript
// ❌ Bad - One large interface
class Machine {
  print() {}
  scan() {}
  fax() {}
  staple() {}
}

class OldPrinter extends Machine {
  print() {
    console.log('Printing...');
  }
  
  scan() {
    throw new Error('This printer cannot scan');
  }
  
  fax() {
    throw new Error('This printer cannot fax');
  }
  
  staple() {
    throw new Error('This printer cannot staple');
  }
}

// ✅ Good - Segregated interfaces
class Printer {
  print(document) {
    throw new Error('Must implement print()');
  }
}

class Scanner {
  scan(document) {
    throw new Error('Must implement scan()');
  }
}

class FaxMachine {
  fax(document) {
    throw new Error('Must implement fax()');
  }
}

// Simple printer only implements what it needs
class SimplePrinter extends Printer {
  print(document) {
    console.log(`Printing: ${document}`);
  }
}

// Modern multi-function printer implements multiple interfaces
class MultiFunctionPrinter {
  constructor() {
    this.printer = new PrinterImpl();
    this.scanner = new ScannerImpl();
    this.fax = new FaxImpl();
  }
  
  print(document) {
    this.printer.print(document);
  }
  
  scan(document) {
    this.scanner.scan(document);
  }
  
  fax(document) {
    this.fax.fax(document);
  }
}

class PrinterImpl extends Printer {
  print(document) {
    console.log(`Printing: ${document}`);
  }
}

class ScannerImpl extends Scanner {
  scan(document) {
    console.log(`Scanning: ${document}`);
  }
}

class FaxImpl extends FaxMachine {
  fax(document) {
    console.log(`Faxing: ${document}`);
  }
}
```

---

### Q51.5: Dependency Inversion Principle (DIP)

**Definition:** 
- High-level modules should not depend on low-level modules. Both should depend on abstractions.
- Abstractions should not depend on details. Details should depend on abstractions.

**❌ Bad Example - Direct Dependency:**

```javascript
// Low-level module
class MySQLDatabase {
  connect() {
    console.log('Connecting to MySQL...');
  }
  
  query(sql) {
    console.log(`Executing MySQL query: ${sql}`);
    return [];
  }
}

// High-level module directly depends on low-level module
class UserRepository {
  constructor() {
    this.database = new MySQLDatabase(); // Tight coupling!
  }
  
  getUsers() {
    this.database.connect();
    return this.database.query('SELECT * FROM users');
  }
}

// Problems:
// - Cannot switch to PostgreSQL without modifying UserRepository
// - Hard to test (cannot mock the database)
// - Tight coupling between layers
```

**✅ Good Example - Dependency Injection:**

```javascript
// Abstraction (Interface)
class Database {
  connect() {
    throw new Error('connect() must be implemented');
  }
  
  query(sql) {
    throw new Error('query() must be implemented');
  }
}

// Low-level modules (Details)
class MySQLDatabase extends Database {
  connect() {
    console.log('Connecting to MySQL...');
  }
  
  query(sql) {
    console.log(`Executing MySQL query: ${sql}`);
    return [{ id: 1, name: 'John' }];
  }
}

class PostgreSQLDatabase extends Database {
  connect() {
    console.log('Connecting to PostgreSQL...');
  }
  
  query(sql) {
    console.log(`Executing PostgreSQL query: ${sql}`);
    return [{ id: 1, name: 'Jane' }];
  }
}

class MongoDatabase extends Database {
  connect() {
    console.log('Connecting to MongoDB...');
  }
  
  query(filter) {
    console.log(`Executing MongoDB query:`, filter);
    return [{ _id: '123', name: 'Bob' }];
  }
}

// High-level module depends on abstraction
class UserRepository {
  constructor(database) {
    this.database = database; // Dependency injection!
  }
  
  getUsers() {
    this.database.connect();
    return this.database.query('SELECT * FROM users');
  }
  
  getUserById(id) {
    this.database.connect();
    return this.database.query(`SELECT * FROM users WHERE id = ${id}`);
  }
}

// Usage - Easy to switch implementations
const mysqlDb = new MySQLDatabase();
const postgresDb = new PostgreSQLDatabase();
const mongoDb = new MongoDatabase();

// Can use any database implementation
const userRepo1 = new UserRepository(mysqlDb);
userRepo1.getUsers();

const userRepo2 = new UserRepository(postgresDb);
userRepo2.getUsers();

const userRepo3 = new UserRepository(mongoDb);
userRepo3.getUsers();

// Easy to mock for testing
class MockDatabase extends Database {
  connect() {
    console.log('Mock database connected');
  }
  
  query(sql) {
    return [{ id: 999, name: 'Test User' }];
  }
}

const testRepo = new UserRepository(new MockDatabase());
console.log(testRepo.getUsers());
```

**Another Example - Notification System:**

```javascript
// ❌ Bad - Direct dependency
class EmailNotification {
  send(message) {
    console.log(`Sending email: ${message}`);
  }
}

class OrderService {
  constructor() {
    this.notification = new EmailNotification(); // Tight coupling
  }
  
  placeOrder(order) {
    console.log('Order placed');
    this.notification.send('Order confirmation');
  }
}

// ✅ Good - Depends on abstraction
class NotificationService {
  send(message) {
    throw new Error('send() must be implemented');
  }
}

class EmailNotification extends NotificationService {
  send(message) {
    console.log(`📧 Email: ${message}`);
  }
}

class SMSNotification extends NotificationService {
  send(message) {
    console.log(`📱 SMS: ${message}`);
  }
}

class PushNotification extends NotificationService {
  send(message) {
    console.log(`🔔 Push: ${message}`);
  }
}

class SlackNotification extends NotificationService {
  send(message) {
    console.log(`💬 Slack: ${message}`);
  }
}

// High-level module depends on abstraction
class OrderService {
  constructor(notificationService) {
    this.notification = notificationService; // Dependency injection
  }
  
  placeOrder(order) {
    console.log(`Processing order: ${order.id}`);
    this.notification.send(`Order ${order.id} confirmed!`);
  }
}

// Usage - Easy to switch notification methods
const order = { id: '12345', total: 99.99 };

const emailService = new OrderService(new EmailNotification());
emailService.placeOrder(order);

const smsService = new OrderService(new SMSNotification());
smsService.placeOrder(order);

const pushService = new OrderService(new PushNotification());
pushService.placeOrder(order);

// Multiple notifications
class MultiNotificationService extends NotificationService {
  constructor(...services) {
    super();
    this.services = services;
  }
  
  send(message) {
    this.services.forEach(service => service.send(message));
  }
}

const multiNotification = new MultiNotificationService(
  new EmailNotification(),
  new SMSNotification(),
  new PushNotification()
);

const multiService = new OrderService(multiNotification);
multiService.placeOrder(order);
// Sends via email, SMS, and push notification!
```

---

### Q51.6: SOLID Principles Summary

**Quick Reference:**

| Principle | Definition | Key Benefit |
|-----------|------------|-------------|
| **Single Responsibility** | One class, one job | Easier to maintain and test |
| **Open/Closed** | Open for extension, closed for modification | Add features without breaking existing code |
| **Liskov Substitution** | Subtypes must be substitutable | Reliable inheritance hierarchies |
| **Interface Segregation** | Many specific interfaces > one general | No forced unused methods |
| **Dependency Inversion** | Depend on abstractions, not concrete classes | Flexible, testable, loosely coupled |

**When to Apply SOLID:**

```javascript
// ✅ Apply SOLID when:
// - Building large applications
// - Code will be maintained long-term
// - Multiple developers on the team
// - Need testability
// - Requirements change frequently

// ⚠️ Consider trade-offs for:
// - Small scripts
// - Prototypes
// - One-time use code
// - Very simple applications
```

**Real-World Example - E-commerce System:**

```javascript
// Single Responsibility
class Product {
  constructor(id, name, price) {
    this.id = id;
    this.name = name;
    this.price = price;
  }
}

class ProductRepository {
  save(product) { /* DB logic */ }
  findById(id) { /* DB logic */ }
}

// Open/Closed
class PaymentProcessor {
  process(paymentMethod, amount) {
    return paymentMethod.charge(amount);
  }
}

// Liskov Substitution
class PaymentMethod {
  charge(amount) { throw new Error('Must implement'); }
}

class CreditCard extends PaymentMethod {
  charge(amount) { /* CC logic */ }
}

// Interface Segregation
class Shippable {
  ship() { throw new Error('Must implement'); }
}

class Trackable {
  track() { throw new Error('Must implement'); }
}

// Dependency Inversion
class OrderService {
  constructor(repo, payment, logger) {
    this.repo = repo;        // Injected
    this.payment = payment;  // Injected
    this.logger = logger;    // Injected
  }
  
  createOrder(order) {
    this.logger.log('Creating order');
    this.repo.save(order);
    this.payment.process(order.paymentMethod, order.total);
  }
}
```

**Benefits of Following SOLID:**
- ✅ Maintainable code
- ✅ Testable components
- ✅ Flexible architecture
- ✅ Reusable code
- ✅ Easier refactoring
- ✅ Better team collaboration
- ✅ Reduced technical debt

---

## Essential JavaScript Built-in Functions & Methods

### Array Methods

#### 1. **map()** - Transform Array Elements

Creates a new array by applying a function to each element.

```javascript
const numbers = [1, 2, 3, 4];
const doubled = numbers.map(num => num * 2);
console.log(doubled); // [2, 4, 6, 8]

// Use case: Transform data from API
const users = [
  { id: 1, firstName: 'John', lastName: 'Doe' },
  { id: 2, firstName: 'Jane', lastName: 'Smith' }
];

const fullNames = users.map(user => `${user.firstName} ${user.lastName}`);
// ['John Doe', 'Jane Smith']

// Extract specific properties
const ids = users.map(user => user.id); // [1, 2]
```

**When to use:** When you need to transform each element without changing the original array.

---

#### 2. **filter()** - Filter Array Elements

Creates a new array with elements that pass a test.

```javascript
const numbers = [1, 2, 3, 4, 5, 6];
const evens = numbers.filter(num => num % 2 === 0);
console.log(evens); // [2, 4, 6]

// Use case: Filter active users
const users = [
  { name: 'Alice', active: true },
  { name: 'Bob', active: false },
  { name: 'Charlie', active: true }
];

const activeUsers = users.filter(user => user.active);
// [{ name: 'Alice', active: true }, { name: 'Charlie', active: true }]

// Complex filtering
const products = [
  { name: 'Laptop', price: 1000, inStock: true },
  { name: 'Phone', price: 500, inStock: false },
  { name: 'Tablet', price: 300, inStock: true }
];

const affordableInStock = products.filter(p => p.price < 600 && p.inStock);
// [{ name: 'Tablet', price: 300, inStock: true }]
```

**When to use:** When you need to get a subset of array based on conditions.

---

#### 3. **reduce()** - Reduce Array to Single Value

Executes a reducer function on each element, resulting in a single output value.

```javascript
// Sum of numbers
const numbers = [1, 2, 3, 4];
const sum = numbers.reduce((acc, num) => acc + num, 0);
console.log(sum); // 10

// Use case: Calculate total price
const cart = [
  { product: 'Laptop', price: 1000, quantity: 1 },
  { product: 'Mouse', price: 50, quantity: 2 }
];

const total = cart.reduce((sum, item) => {
  return sum + (item.price * item.quantity);
}, 0);
console.log(total); // 1100

// Group by category
const items = [
  { name: 'Apple', category: 'Fruit' },
  { name: 'Carrot', category: 'Vegetable' },
  { name: 'Banana', category: 'Fruit' }
];

const grouped = items.reduce((acc, item) => {
  if (!acc[item.category]) {
    acc[item.category] = [];
  }
  acc[item.category].push(item.name);
  return acc;
}, {});
// { Fruit: ['Apple', 'Banana'], Vegetable: ['Carrot'] }

// Count occurrences
const fruits = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple'];
const count = fruits.reduce((acc, fruit) => {
  acc[fruit] = (acc[fruit] || 0) + 1;
  return acc;
}, {});
// { apple: 3, banana: 2, orange: 1 }

// Flatten array
const nested = [[1, 2], [3, 4], [5, 6]];
const flattened = nested.reduce((acc, arr) => acc.concat(arr), []);
// [1, 2, 3, 4, 5, 6]
```

**When to use:** When you need to transform an array into a single value or different structure.

---

#### 4. **find()** - Find First Matching Element

Returns the first element that satisfies the condition.

```javascript
const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' },
  { id: 3, name: 'Charlie' }
];

const user = users.find(u => u.id === 2);
console.log(user); // { id: 2, name: 'Bob' }

// Returns undefined if not found
const notFound = users.find(u => u.id === 99);
console.log(notFound); // undefined

// Use case: Find product by ID
const product = products.find(p => p.id === searchId);
if (product) {
  console.log(`Found: ${product.name}`);
} else {
  console.log('Product not found');
}
```

**When to use:** When you need the first element matching a condition.

---

#### 5. **findIndex()** - Find Index of First Match

Returns the index of the first element that satisfies the condition.

```javascript
const numbers = [5, 12, 8, 130, 44];
const index = numbers.findIndex(num => num > 10);
console.log(index); // 1 (number 12)

// Returns -1 if not found
const notFound = numbers.findIndex(num => num > 200);
console.log(notFound); // -1

// Use case: Update item in array
const todos = [
  { id: 1, task: 'Buy milk', done: false },
  { id: 2, task: 'Clean room', done: false }
];

const indexToUpdate = todos.findIndex(todo => todo.id === 2);
if (indexToUpdate !== -1) {
  todos[indexToUpdate].done = true;
}
```

**When to use:** When you need the position of an element for updating or removing.

---

#### 6. **some()** - Check If Any Element Passes Test

Returns true if at least one element satisfies the condition.

```javascript
const numbers = [1, 2, 3, 4, 5];
const hasEven = numbers.some(num => num % 2 === 0);
console.log(hasEven); // true

// Use case: Validate if any field is empty
const form = {
  name: 'John',
  email: '',
  phone: '123456'
};

const hasEmptyField = Object.values(form).some(value => value === '');
console.log(hasEmptyField); // true

// Check if user has admin permission
const permissions = ['read', 'write', 'admin'];
const isAdmin = permissions.some(p => p === 'admin');
```

**When to use:** When you need to check if at least one element meets a condition.

---

#### 7. **every()** - Check If All Elements Pass Test

Returns true if all elements satisfy the condition.

```javascript
const numbers = [2, 4, 6, 8];
const allEven = numbers.every(num => num % 2 === 0);
console.log(allEven); // true

// Use case: Validate all fields are filled
const form = {
  name: 'John',
  email: 'john@example.com',
  phone: '123456'
};

const allFilled = Object.values(form).every(value => value !== '');
console.log(allFilled); // true

// Check if all items are in stock
const cart = [
  { product: 'A', inStock: true },
  { product: 'B', inStock: true }
];

const canCheckout = cart.every(item => item.inStock);
```

**When to use:** When you need to verify all elements meet a condition.

---

#### 8. **forEach()** - Execute Function for Each Element

Executes a function for each array element (no return value).

```javascript
const numbers = [1, 2, 3];
numbers.forEach(num => console.log(num * 2));
// 2
// 4
// 6

// Use case: Update DOM elements
const items = ['Apple', 'Banana', 'Orange'];
items.forEach((item, index) => {
  const li = document.createElement('li');
  li.textContent = `${index + 1}. ${item}`;
  document.querySelector('ul').appendChild(li);
});

// Side effects
const users = [{ name: 'Alice', points: 10 }];
users.forEach(user => {
  user.bonus = user.points * 0.1; // Mutates original array
});
```

**When to use:** When you need to perform side effects for each element (not creating new array).

**Note:** Use `map()` if you need to transform and return a new array.

---

#### 9. **slice()** - Extract Portion of Array

Returns a shallow copy of a portion of an array.

```javascript
const fruits = ['Apple', 'Banana', 'Orange', 'Mango', 'Pineapple'];

// Extract from index 1 to 3 (end not included)
const sliced = fruits.slice(1, 3);
console.log(sliced); // ['Banana', 'Orange']

// From index 2 to end
const fromTwo = fruits.slice(2);
console.log(fromTwo); // ['Orange', 'Mango', 'Pineapple']

// Last 2 elements
const lastTwo = fruits.slice(-2);
console.log(lastTwo); // ['Mango', 'Pineapple']

// Copy entire array
const copy = fruits.slice();

// Use case: Pagination
const items = [...Array(100)].map((_, i) => `Item ${i + 1}`);
const page = 2;
const pageSize = 10;
const start = (page - 1) * pageSize;
const pageItems = items.slice(start, start + pageSize);
```

**When to use:** When you need to extract a portion without modifying the original array.

---

#### 10. **splice()** - Add/Remove Elements (Mutates Array)

Changes the contents of an array by removing or replacing existing elements.

```javascript
const fruits = ['Apple', 'Banana', 'Orange', 'Mango'];

// Remove 2 elements starting from index 1
const removed = fruits.splice(1, 2);
console.log(removed); // ['Banana', 'Orange']
console.log(fruits);  // ['Apple', 'Mango']

// Insert elements
fruits.splice(1, 0, 'Grape', 'Kiwi');
console.log(fruits); // ['Apple', 'Grape', 'Kiwi', 'Mango']

// Replace elements
fruits.splice(1, 2, 'Pear');
console.log(fruits); // ['Apple', 'Pear', 'Mango']

// Use case: Remove item by ID
const todos = [
  { id: 1, task: 'Task 1' },
  { id: 2, task: 'Task 2' },
  { id: 3, task: 'Task 3' }
];

const indexToRemove = todos.findIndex(t => t.id === 2);
if (indexToRemove !== -1) {
  todos.splice(indexToRemove, 1);
}
```

**When to use:** When you need to modify the original array (add/remove elements).

**Warning:** Mutates the original array. Use with caution in React/Redux.

---

#### 11. **concat()** - Merge Arrays

Merges two or more arrays without modifying existing arrays.

```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];
const arr3 = [5, 6];

const merged = arr1.concat(arr2, arr3);
console.log(merged); // [1, 2, 3, 4, 5, 6]

// Modern alternative: Spread operator
const merged2 = [...arr1, ...arr2, ...arr3];

// Concat with values
const withValues = arr1.concat(10, 20, arr2);
console.log(withValues); // [1, 2, 10, 20, 3, 4]
```

**When to use:** When you need to combine arrays immutably.

---

#### 12. **join()** - Convert Array to String

Joins all elements into a string with a separator.

```javascript
const words = ['Hello', 'World', '!'];
const sentence = words.join(' ');
console.log(sentence); // 'Hello World !'

// CSV format
const data = ['John', 'Doe', '30', 'Developer'];
const csv = data.join(',');
console.log(csv); // 'John,Doe,30,Developer'

// Use case: Create URL path
const pathParts = ['api', 'users', '123', 'posts'];
const url = pathParts.join('/');
console.log(url); // 'api/users/123/posts'

// Join with no separator
const letters = ['H', 'e', 'l', 'l', 'o'];
const word = letters.join('');
console.log(word); // 'Hello'
```

**When to use:** When you need to convert array elements to a single string.

---

#### 13. **includes()** - Check If Array Contains Value

Determines whether an array includes a certain value.

```javascript
const fruits = ['Apple', 'Banana', 'Orange'];

console.log(fruits.includes('Banana')); // true
console.log(fruits.includes('Grape'));  // false

// Case sensitive
console.log(fruits.includes('apple')); // false

// Use case: Check user role
const userRoles = ['user', 'editor', 'admin'];
const hasAdminAccess = userRoles.includes('admin');

// Check from specific index
const numbers = [1, 2, 3, 4, 5];
console.log(numbers.includes(3, 2)); // true (search from index 2)
console.log(numbers.includes(2, 2)); // false (2 is before index 2)
```

**When to use:** When you need to check if a value exists in an array.

---

#### 14. **indexOf()** - Find Index of Element

Returns the first index of a specified value, or -1 if not found.

```javascript
const fruits = ['Apple', 'Banana', 'Orange', 'Banana'];

console.log(fruits.indexOf('Banana')); // 1
console.log(fruits.indexOf('Grape'));  // -1

// Find from specific index
console.log(fruits.indexOf('Banana', 2)); // 3

// Use case: Check if exists and remove
const items = ['a', 'b', 'c'];
const index = items.indexOf('b');
if (index > -1) {
  items.splice(index, 1); // Remove 'b'
}
```

**When to use:** When you need the position of a primitive value in an array.

---

#### 15. **sort()** - Sort Array (Mutates)

Sorts elements of an array in place.

```javascript
// String sorting (alphabetical)
const fruits = ['Banana', 'Apple', 'Orange'];
fruits.sort();
console.log(fruits); // ['Apple', 'Banana', 'Orange']

// Number sorting (requires comparator!)
const numbers = [10, 5, 40, 25, 1000];

// ❌ Wrong - treats as strings
numbers.sort();
console.log(numbers); // [10, 1000, 25, 40, 5]

// ✅ Correct - use comparator
numbers.sort((a, b) => a - b); // Ascending
console.log(numbers); // [5, 10, 25, 40, 1000]

numbers.sort((a, b) => b - a); // Descending
console.log(numbers); // [1000, 40, 25, 10, 5]

// Sort objects
const users = [
  { name: 'Charlie', age: 30 },
  { name: 'Alice', age: 25 },
  { name: 'Bob', age: 35 }
];

// Sort by name
users.sort((a, b) => a.name.localeCompare(b.name));

// Sort by age
users.sort((a, b) => a.age - b.age);

// Multi-level sorting
users.sort((a, b) => {
  if (a.age !== b.age) {
    return a.age - b.age; // Sort by age first
  }
  return a.name.localeCompare(b.name); // Then by name
});

// Immutable sort
const sorted = [...numbers].sort((a, b) => a - b);
```

**When to use:** When you need to order array elements.

**Warning:** Mutates the original array. Create a copy first for immutability.

---

#### 16. **reverse()** - Reverse Array (Mutates)

Reverses the order of elements in an array.

```javascript
const numbers = [1, 2, 3, 4, 5];
numbers.reverse();
console.log(numbers); // [5, 4, 3, 2, 1]

// Immutable reverse
const original = [1, 2, 3];
const reversed = [...original].reverse();
console.log(original); // [1, 2, 3] (unchanged)
console.log(reversed); // [3, 2, 1]

// Use case: Reverse string
const str = 'Hello';
const reversedStr = str.split('').reverse().join('');
console.log(reversedStr); // 'olleH'
```

**When to use:** When you need to reverse the order of elements.

---

#### 17. **flat()** - Flatten Nested Arrays

Flattens nested arrays to a specified depth.

```javascript
const nested = [1, [2, 3], [4, [5, 6]]];

// Flatten one level (default)
console.log(nested.flat()); // [1, 2, 3, 4, [5, 6]]

// Flatten two levels
console.log(nested.flat(2)); // [1, 2, 3, 4, 5, 6]

// Flatten all levels
console.log(nested.flat(Infinity)); // [1, 2, 3, 4, 5, 6]

// Remove empty slots
const withHoles = [1, 2, , 4, 5];
console.log(withHoles.flat()); // [1, 2, 4, 5]

// Use case: Flatten array of arrays from API
const responses = [
  [{ id: 1 }, { id: 2 }],
  [{ id: 3 }, { id: 4 }],
  [{ id: 5 }]
];
const allItems = responses.flat();
// [{ id: 1 }, { id: 2 }, { id: 3 }, { id: 4 }, { id: 5 }]
```

**When to use:** When you have nested arrays and need a single-level array.

---

#### 18. **flatMap()** - Map + Flatten

Maps each element then flattens the result.

```javascript
const sentences = ['Hello world', 'How are you'];

// Split each sentence into words
const words = sentences.flatMap(sentence => sentence.split(' '));
console.log(words); // ['Hello', 'world', 'How', 'are', 'you']

// Equivalent to:
const words2 = sentences.map(s => s.split(' ')).flat();

// Use case: Get all tags from posts
const posts = [
  { title: 'Post 1', tags: ['js', 'react'] },
  { title: 'Post 2', tags: ['node', 'express'] },
  { title: 'Post 3', tags: ['react', 'redux'] }
];

const allTags = posts.flatMap(post => post.tags);
// ['js', 'react', 'node', 'express', 'react', 'redux']

const uniqueTags = [...new Set(allTags)];
// ['js', 'react', 'node', 'express', 'redux']

// Filter and map in one operation
const numbers = [1, 2, 3, 4];
const result = numbers.flatMap(n => n % 2 === 0 ? [n, n * 2] : []);
console.log(result); // [2, 4, 4, 8]
```

**When to use:** When you need to map and flatten in one operation.

---

#### 19. **fill()** - Fill Array with Value

Fills array elements with a static value.

```javascript
const arr = new Array(5).fill(0);
console.log(arr); // [0, 0, 0, 0, 0]

// Fill from index 2 to 4
const arr2 = [1, 2, 3, 4, 5];
arr2.fill(0, 2, 4);
console.log(arr2); // [1, 2, 0, 0, 5]

// ⚠️ Warning with objects - fills with same reference!
const arr3 = new Array(3).fill({});
arr3[0].name = 'John';
console.log(arr3); // [{ name: 'John' }, { name: 'John' }, { name: 'John' }]

// ✅ Correct way for objects
const arr4 = Array.from({ length: 3 }, () => ({}));
arr4[0].name = 'John';
console.log(arr4); // [{ name: 'John' }, {}, {}]
```

**When to use:** When you need to initialize an array with a default value.

---

#### 20. **from()** - Create Array from Iterable

Creates a new array from an array-like or iterable object.

```javascript
// From string
const str = 'Hello';
const chars = Array.from(str);
console.log(chars); // ['H', 'e', 'l', 'l', 'o']

// From Set
const set = new Set([1, 2, 3, 3, 4]);
const arr = Array.from(set);
console.log(arr); // [1, 2, 3, 4]

// From NodeList
const divs = document.querySelectorAll('div');
const divsArray = Array.from(divs);
divsArray.forEach(div => console.log(div));

// With mapping function
const numbers = Array.from({ length: 5 }, (_, i) => i + 1);
console.log(numbers); // [1, 2, 3, 4, 5]

// Create array of objects
const users = Array.from({ length: 3 }, (_, i) => ({
  id: i + 1,
  name: `User ${i + 1}`
}));
// [{ id: 1, name: 'User 1' }, { id: 2, name: 'User 2' }, ...]

// From arguments object
function sum() {
  const args = Array.from(arguments);
  return args.reduce((a, b) => a + b, 0);
}
```

**When to use:** When you need to convert an iterable or array-like object to an actual array.

---

### String Methods

#### 21. **split()** - Convert String to Array

Splits a string into an array of substrings.

```javascript
const sentence = 'Hello World JavaScript';
const words = sentence.split(' ');
console.log(words); // ['Hello', 'World', 'JavaScript']

// Split by each character
const chars = 'Hello'.split('');
console.log(chars); // ['H', 'e', 'l', 'l', 'o']

// Limit the number of splits
const limited = sentence.split(' ', 2);
console.log(limited); // ['Hello', 'World']

// Use case: Parse CSV
const csv = 'John,Doe,30,Developer';
const fields = csv.split(',');
// ['John', 'Doe', '30', 'Developer']

// Use case: Parse URL path
const path = '/api/users/123/posts';
const segments = path.split('/').filter(Boolean);
// ['api', 'users', '123', 'posts']

// Split by regex
const text = 'apple, banana; orange|grape';
const fruits = text.split(/[,;|]/);
// ['apple', ' banana', ' orange', 'grape']
```

**When to use:** When you need to break a string into parts.

---

#### 22. **substring() / slice()** - Extract Part of String

Extracts a section of a string.

```javascript
const str = 'Hello World';

// substring(start, end)
console.log(str.substring(0, 5)); // 'Hello'
console.log(str.substring(6));    // 'World'

// slice(start, end) - similar but handles negative indices
console.log(str.slice(0, 5));  // 'Hello'
console.log(str.slice(-5));    // 'World' (last 5 characters)
console.log(str.slice(-5, -1)); // 'Worl'

// Use case: Get file extension
const filename = 'document.pdf';
const ext = filename.slice(filename.lastIndexOf('.') + 1);
console.log(ext); // 'pdf'

// Use case: Truncate text
const longText = 'This is a very long text that needs truncation';
const truncated = longText.slice(0, 20) + '...';
console.log(truncated); // 'This is a very long ...'
```

**When to use:** When you need to extract a portion of a string.

---

#### 23. **replace() / replaceAll()** - Replace String Content

Replaces specified values in a string.

```javascript
const text = 'Hello World';

// Replace first occurrence
const replaced = text.replace('World', 'JavaScript');
console.log(replaced); // 'Hello JavaScript'

// Replace all (regex with g flag)
const text2 = 'cat, cat, cat';
const allReplaced = text2.replace(/cat/g, 'dog');
console.log(allReplaced); // 'dog, dog, dog'

// replaceAll() - modern method
const allReplaced2 = text2.replaceAll('cat', 'dog');
console.log(allReplaced2); // 'dog, dog, dog'

// Use case: Remove special characters
const input = 'Hello! World? 123';
const cleaned = input.replace(/[^a-zA-Z0-9\s]/g, '');
console.log(cleaned); // 'Hello World 123'

// Use case: Replace with function
const template = 'Hello {name}, you are {age} years old';
const data = { name: 'John', age: 30 };
const result = template.replace(/{(\w+)}/g, (match, key) => data[key]);
console.log(result); // 'Hello John, you are 30 years old'

// Case-insensitive replace
const text3 = 'Hello hello HELLO';
const replaced3 = text3.replace(/hello/gi, 'Hi');
console.log(replaced3); // 'Hi Hi Hi'
```

**When to use:** When you need to modify parts of a string.

---

#### 24. **trim() / trimStart() / trimEnd()** - Remove Whitespace

Removes whitespace from the beginning and/or end of a string.

```javascript
const str = '  Hello World  ';

console.log(str.trim());      // 'Hello World'
console.log(str.trimStart()); // 'Hello World  '
console.log(str.trimEnd());   // '  Hello World'

// Use case: Clean user input
const userInput = '  john@example.com  ';
const email = userInput.trim().toLowerCase();
console.log(email); // 'john@example.com'

// Clean all whitespace in array
const inputs = ['  Alice  ', '  Bob  ', '  Charlie  '];
const cleaned = inputs.map(s => s.trim());
// ['Alice', 'Bob', 'Charlie']
```

**When to use:** When processing user input or cleaning strings.

---

#### 25. **toUpperCase() / toLowerCase()** - Change Case

Converts string to uppercase or lowercase.

```javascript
const str = 'Hello World';

console.log(str.toUpperCase()); // 'HELLO WORLD'
console.log(str.toLowerCase()); // 'hello world'

// Use case: Case-insensitive comparison
const input1 = 'JavaScript';
const input2 = 'javascript';
console.log(input1.toLowerCase() === input2.toLowerCase()); // true

// Use case: Capitalize first letter
function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1).toLowerCase();
}
console.log(capitalize('hELLO')); // 'Hello'

// Title case
function titleCase(str) {
  return str
    .toLowerCase()
    .split(' ')
    .map(word => word.charAt(0).toUpperCase() + word.slice(1))
    .join(' ');
}
console.log(titleCase('hello world')); // 'Hello World'
```

**When to use:** When you need to normalize or format text.

---

#### 26. **includes() / startsWith() / endsWith()** - Check String Content

Checks if a string contains, starts, or ends with specified text.

```javascript
const str = 'Hello World';

// includes - check if contains
console.log(str.includes('World')); // true
console.log(str.includes('world')); // false (case-sensitive)

// startsWith
console.log(str.startsWith('Hello')); // true
console.log(str.startsWith('World')); // false

// endsWith
console.log(str.endsWith('World')); // true
console.log(str.endsWith('Hello')); // false

// Use case: Validate URL
const url = 'https://example.com/api/users';
const isHttps = url.startsWith('https://');
const isApiEndpoint = url.includes('/api/');
console.log(isHttps, isApiEndpoint); // true, true

// Use case: File type validation
const filename = 'document.pdf';
const isPDF = filename.toLowerCase().endsWith('.pdf');

// Check from position
console.log(str.includes('World', 7)); // false (search from index 7)
console.log(str.startsWith('World', 6)); // true (check from index 6)
```

**When to use:** When you need to check string content without regex.

---

#### 27. **match() / matchAll()** - Match Against Regex

Returns matches of a string against a regular expression.

```javascript
const text = 'My number is 123-456-7890 and 098-765-4321';

// match - get first match
const match = text.match(/\d{3}-\d{3}-\d{4}/);
console.log(match[0]); // '123-456-7890'

// match with g flag - get all matches
const allMatches = text.match(/\d{3}-\d{3}-\d{4}/g);
console.log(allMatches); // ['123-456-7890', '098-765-4321']

// matchAll - get detailed matches with groups
const text2 = 'John: 25, Jane: 30, Bob: 35';
const regex = /(\w+): (\d+)/g;
const matches = [...text2.matchAll(regex)];
matches.forEach(match => {
  console.log(`Name: ${match[1]}, Age: ${match[2]}`);
});
// Name: John, Age: 25
// Name: Jane, Age: 30
// Name: Bob, Age: 35

// Use case: Extract emails
const text3 = 'Contact: john@example.com or jane@example.com';
const emails = text3.match(/[\w.-]+@[\w.-]+\.\w+/g);
console.log(emails); // ['john@example.com', 'jane@example.com']

// Extract hashtags
const tweet = 'Loving #JavaScript and #WebDev today!';
const hashtags = tweet.match(/#\w+/g);
console.log(hashtags); // ['#JavaScript', '#WebDev']
```

**When to use:** When you need to extract patterns from strings using regex.

---

#### 28. **padStart() / padEnd()** - Pad String

Pads a string to a specified length.

```javascript
const num = '5';

// Pad with zeros
console.log(num.padStart(3, '0')); // '005'
console.log(num.padEnd(3, '0'));   // '500'

// Use case: Format time
const hours = '9';
const minutes = '5';
const time = `${hours.padStart(2, '0')}:${minutes.padStart(2, '0')}`;
console.log(time); // '09:05'

// Use case: Format invoice numbers
const invoiceNum = 42;
const formatted = String(invoiceNum).padStart(6, '0');
console.log(`INV-${formatted}`); // 'INV-000042'

// Align text
const items = ['Apple', 'Banana', 'Kiwi'];
items.forEach(item => {
  console.log(item.padEnd(10, '.') + ' $2.99');
});
// Apple..... $2.99
// Banana.... $2.99
// Kiwi...... $2.99
```

**When to use:** When you need to format strings to a fixed length.

---

#### 29. **repeat()** - Repeat String

Repeats a string a specified number of times.

```javascript
const str = 'Ha';
console.log(str.repeat(3)); // 'HaHaHa'

// Use case: Create visual separator
const separator = '-'.repeat(50);
console.log(separator); // '--------------------------------------------------'

// Use case: Indentation
function printTree(level, text) {
  const indent = '  '.repeat(level);
  console.log(`${indent}${text}`);
}
printTree(0, 'Root');
printTree(1, 'Child 1');
printTree(2, 'Grandchild');

// Create loading animation
const loadingStates = ['.', '..', '...'].map(dots => `Loading${dots}`);
```

**When to use:** When you need to duplicate a string pattern.

---

#### 30. **charAt() / charCodeAt()** - Get Character

Gets character or character code at a specified index.

```javascript
const str = 'Hello';

console.log(str.charAt(0));     // 'H'
console.log(str.charAt(4));     // 'o'
console.log(str[0]);            // 'H' (bracket notation)

console.log(str.charCodeAt(0)); // 72 (ASCII code for 'H')
console.log(str.charCodeAt(4)); // 111 (ASCII code for 'o')

// Use case: Check if uppercase
function isUpperCase(char) {
  return char === char.toUpperCase() && char !== char.toLowerCase();
}
console.log(isUpperCase(str.charAt(0))); // true

// Use case: Generate random string
function randomString(length) {
  const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
  let result = '';
  for (let i = 0; i < length; i++) {
    result += chars.charAt(Math.floor(Math.random() * chars.length));
  }
  return result;
}
```

**When to use:** When you need to access individual characters.

---

### Object Methods

#### 31. **Object.keys()** - Get Object Keys

Returns an array of an object's own property names.

```javascript
const user = {
  name: 'John',
  age: 30,
  email: 'john@example.com'
};

const keys = Object.keys(user);
console.log(keys); // ['name', 'age', 'email']

// Use case: Iterate over object properties
Object.keys(user).forEach(key => {
  console.log(`${key}: ${user[key]}`);
});
// name: John
// age: 30
// email: john@example.com

// Use case: Check if object is empty
const isEmpty = obj => Object.keys(obj).length === 0;
console.log(isEmpty({})); // true
console.log(isEmpty(user)); // false

// Use case: Filter object properties
const product = { id: 1, name: 'Laptop', price: 1000, inStock: true };
const filtered = {};
Object.keys(product)
  .filter(key => key !== 'id')
  .forEach(key => filtered[key] = product[key]);
// { name: 'Laptop', price: 1000, inStock: true }
```

**When to use:** When you need to iterate over or work with object property names.

---

#### 32. **Object.values()** - Get Object Values

Returns an array of an object's own property values.

```javascript
const user = {
  name: 'John',
  age: 30,
  email: 'john@example.com'
};

const values = Object.values(user);
console.log(values); // ['John', 30, 'john@example.com']

// Use case: Sum all numeric values
const scores = { math: 90, science: 85, english: 92 };
const total = Object.values(scores).reduce((sum, score) => sum + score, 0);
console.log(total); // 267

// Use case: Check if any value is empty
const form = { name: 'John', email: '', phone: '123' };
const hasEmpty = Object.values(form).some(value => value === '');
console.log(hasEmpty); // true

// Use case: Get all users from grouped data
const usersByRole = {
  admin: ['Alice', 'Bob'],
  user: ['Charlie', 'David'],
  guest: ['Eve']
};
const allUsers = Object.values(usersByRole).flat();
// ['Alice', 'Bob', 'Charlie', 'David', 'Eve']
```

**When to use:** When you need to work with object values without their keys.

---

#### 33. **Object.entries()** - Get Key-Value Pairs

Returns an array of an object's own [key, value] pairs.

```javascript
const user = {
  name: 'John',
  age: 30,
  email: 'john@example.com'
};

const entries = Object.entries(user);
console.log(entries);
// [['name', 'John'], ['age', 30], ['email', 'john@example.com']]

// Use case: Convert to Map
const map = new Map(Object.entries(user));

// Use case: Iterate with both key and value
Object.entries(user).forEach(([key, value]) => {
  console.log(`${key}: ${value}`);
});

// Use case: Filter and reconstruct object
const product = { id: 1, name: 'Laptop', price: 1000, discount: 0 };
const filtered = Object.fromEntries(
  Object.entries(product).filter(([key, value]) => value !== 0)
);
// { id: 1, name: 'Laptop', price: 1000 }

// Use case: Transform object values
const prices = { apple: 1, banana: 0.5, orange: 0.75 };
const doubled = Object.fromEntries(
  Object.entries(prices).map(([key, value]) => [key, value * 2])
);
// { apple: 2, banana: 1, orange: 1.5 }

// Use case: Swap keys and values
const original = { a: 1, b: 2, c: 3 };
const swapped = Object.fromEntries(
  Object.entries(original).map(([key, value]) => [value, key])
);
// { 1: 'a', 2: 'b', 3: 'c' }
```

**When to use:** When you need both keys and values for transformation or iteration.

---

#### 34. **Object.assign()** - Merge Objects

Copies properties from source objects to a target object.

```javascript
const target = { a: 1, b: 2 };
const source = { b: 3, c: 4 };

const result = Object.assign(target, source);
console.log(result); // { a: 1, b: 3, c: 4 }
console.log(target); // { a: 1, b: 3, c: 4 } (target is mutated!)

// Immutable merge (common pattern)
const obj1 = { a: 1 };
const obj2 = { b: 2 };
const merged = Object.assign({}, obj1, obj2);
// or use spread operator (preferred)
const merged2 = { ...obj1, ...obj2 };

// Use case: Add default properties
const defaults = { color: 'blue', size: 'medium' };
const userPrefs = { color: 'red' };
const settings = { ...defaults, ...userPrefs };
// { color: 'red', size: 'medium' }

// Use case: Clone object (shallow)
const original = { name: 'John', age: 30 };
const clone = Object.assign({}, original);

// Merge multiple objects
const obj3 = { a: 1 };
const obj4 = { b: 2 };
const obj5 = { c: 3 };
const combined = Object.assign({}, obj3, obj4, obj5);
// { a: 1, b: 2, c: 3 }
```

**When to use:** When you need to merge objects (prefer spread operator in modern code).

---

#### 35. **Object.freeze()** - Make Object Immutable

Freezes an object, preventing modifications.

```javascript
const user = {
  name: 'John',
  age: 30
};

Object.freeze(user);

// These modifications will fail silently (or throw in strict mode)
user.age = 31;        // Fails
user.email = 'test';  // Fails
delete user.name;     // Fails

console.log(user); // { name: 'John', age: 30 } (unchanged)

// Check if frozen
console.log(Object.isFrozen(user)); // true

// ⚠️ Shallow freeze - nested objects not frozen
const data = {
  user: { name: 'John' },
  settings: { theme: 'dark' }
};
Object.freeze(data);

data.user.name = 'Jane'; // This works! Nested object not frozen
console.log(data.user.name); // 'Jane'

// Deep freeze function
function deepFreeze(obj) {
  Object.freeze(obj);
  Object.values(obj).forEach(value => {
    if (typeof value === 'object' && value !== null) {
      deepFreeze(value);
    }
  });
  return obj;
}

// Use case: Constants
const CONFIG = Object.freeze({
  API_URL: 'https://api.example.com',
  TIMEOUT: 5000
});
```

**When to use:** When you need to ensure an object cannot be modified.

---

#### 36. **Object.seal()** - Seal Object

Prevents adding/removing properties but allows modifying existing ones.

```javascript
const user = {
  name: 'John',
  age: 30
};

Object.seal(user);

// Can modify existing properties
user.age = 31; // ✅ Works
console.log(user.age); // 31

// Cannot add new properties
user.email = 'test'; // ❌ Fails
console.log(user.email); // undefined

// Cannot delete properties
delete user.name; // ❌ Fails
console.log(user.name); // 'John'

console.log(Object.isSealed(user)); // true

// Use case: Ensure object structure remains constant
const apiResponse = {
  status: 200,
  data: null,
  error: null
};
Object.seal(apiResponse);
```

**When to use:** When you want to allow value changes but prevent structural changes.

---

#### 37. **Object.create()** - Create Object with Prototype

Creates a new object with the specified prototype.

```javascript
const personPrototype = {
  greet() {
    console.log(`Hello, I'm ${this.name}`);
  }
};

const john = Object.create(personPrototype);
john.name = 'John';
john.greet(); // 'Hello, I'm John'

// Create object without prototype
const obj = Object.create(null); // No prototype chain
console.log(obj.toString); // undefined

// Use case: Inherit methods
const animal = {
  makeSound() {
    console.log(this.sound);
  }
};

const dog = Object.create(animal);
dog.sound = 'Woof';
dog.makeSound(); // 'Woof'

// Create with properties
const person = Object.create(personPrototype, {
  name: {
    value: 'Jane',
    writable: true,
    enumerable: true
  },
  age: {
    value: 25,
    writable: false // Read-only
  }
});
```

**When to use:** When you need to create objects with a specific prototype chain.

---

#### 38. **Object.hasOwnProperty()** - Check Own Property

Checks if an object has a property as its own (not inherited).

```javascript
const user = {
  name: 'John',
  age: 30
};

console.log(user.hasOwnProperty('name')); // true
console.log(user.hasOwnProperty('toString')); // false (inherited)

// Modern alternative: Object.hasOwn()
console.log(Object.hasOwn(user, 'name')); // true

// Use case: Safe property access
function safeGet(obj, key, defaultValue) {
  return obj.hasOwnProperty(key) ? obj[key] : defaultValue;
}

// Use case: Filter own properties
const obj = Object.create({ inherited: 'value' });
obj.own = 'own value';

for (let key in obj) {
  if (obj.hasOwnProperty(key)) {
    console.log(key, obj[key]); // Only logs: own own value
  }
}
```

**When to use:** When you need to distinguish own properties from inherited ones.

---

#### 39. **Object.getOwnPropertyDescriptor()** - Get Property Details

Returns property descriptor for an own property.

```javascript
const obj = {
  name: 'John'
};

const descriptor = Object.getOwnPropertyDescriptor(obj, 'name');
console.log(descriptor);
// {
//   value: 'John',
//   writable: true,
//   enumerable: true,
//   configurable: true
// }

// Define property with custom descriptor
Object.defineProperty(obj, 'age', {
  value: 30,
  writable: false,  // Read-only
  enumerable: true,
  configurable: false // Cannot be deleted or reconfigured
});

obj.age = 31; // Fails silently (or throws in strict mode)
console.log(obj.age); // 30

// Use case: Create getter/setter
Object.defineProperty(obj, 'fullName', {
  get() {
    return `${this.firstName} ${this.lastName}`;
  },
  set(value) {
    [this.firstName, this.lastName] = value.split(' ');
  },
  enumerable: true
});

obj.fullName = 'John Doe';
console.log(obj.firstName); // 'John'
console.log(obj.fullName);  // 'John Doe'
```

**When to use:** When you need fine-grained control over property behavior.

---

### Function Methods

#### 40. **bind()** - Bind Context

Creates a new function with a fixed `this` value.

```javascript
const user = {
  name: 'John',
  greet() {
    console.log(`Hello, ${this.name}`);
  }
};

const greet = user.greet;
greet(); // Error or undefined - lost context

const boundGreet = user.greet.bind(user);
boundGreet(); // 'Hello, John' - context preserved

// Use case: Event handlers
class Button {
  constructor(label) {
    this.label = label;
    this.handleClick = this.handleClick.bind(this); // Bind in constructor
  }
  
  handleClick() {
    console.log(`${this.label} clicked`);
  }
}

const btn = new Button('Submit');
document.querySelector('button').addEventListener('click', btn.handleClick);

// Partial application
function multiply(a, b) {
  return a * b;
}

const double = multiply.bind(null, 2); // Fix first argument
console.log(double(5)); // 10
console.log(double(10)); // 20

const triple = multiply.bind(null, 3);
console.log(triple(5)); // 15
```

**When to use:** When you need to preserve function context or create partial functions.

---

#### 41. **call()** - Call Function with Context

Calls a function with a given `this` value and arguments.

```javascript
function greet(greeting, punctuation) {
  console.log(`${greeting}, ${this.name}${punctuation}`);
}

const user = { name: 'John' };

greet.call(user, 'Hello', '!'); // 'Hello, John!'

// Use case: Borrow methods
const person1 = {
  name: 'Alice',
  greet() {
    console.log(`Hi, I'm ${this.name}`);
  }
};

const person2 = { name: 'Bob' };

person1.greet.call(person2); // 'Hi, I'm Bob'

// Use case: Find max in array (historical)
const numbers = [1, 5, 3, 9, 2];
const max = Math.max.call(null, ...numbers); // or Math.max(...numbers)
console.log(max); // 9

// Use case: Convert array-like to array
function toArray() {
  return Array.prototype.slice.call(arguments);
}
console.log(toArray(1, 2, 3)); // [1, 2, 3]
```

**When to use:** When you need to invoke a function immediately with a specific context.

---

#### 42. **apply()** - Call Function with Arguments Array

Similar to `call()` but takes arguments as an array.

```javascript
function greet(greeting, punctuation) {
  console.log(`${greeting}, ${this.name}${punctuation}`);
}

const user = { name: 'John' };

greet.apply(user, ['Hello', '!']); // 'Hello, John!'

// Use case: Find min/max with array
const numbers = [1, 5, 3, 9, 2];
const max = Math.max.apply(null, numbers);
console.log(max); // 9

// Modern alternative: spread operator
const max2 = Math.max(...numbers);

// Use case: Append array to another
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
Array.prototype.push.apply(arr1, arr2);
console.log(arr1); // [1, 2, 3, 4, 5, 6]

// Modern alternative
arr1.push(...arr2);
```

**When to use:** When you need to call a function with arguments from an array (less common now with spread operator).

---

### Utility Functions

#### 43. **setTimeout() / setInterval()** - Timers

Executes code after a delay or at intervals.

```javascript
// setTimeout - execute once after delay
const timerId = setTimeout(() => {
  console.log('Executed after 2 seconds');
}, 2000);

// Cancel timeout
clearTimeout(timerId);

// setInterval - execute repeatedly
const intervalId = setInterval(() => {
  console.log('Executed every second');
}, 1000);

// Cancel interval
clearInterval(intervalId);

// Use case: Debounce search
function debounce(func, delay) {
  let timeoutId;
  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

const searchInput = document.querySelector('input');
const handleSearch = debounce((e) => {
  console.log('Searching for:', e.target.value);
}, 500);
searchInput.addEventListener('input', handleSearch);

// Use case: Throttle scroll
function throttle(func, limit) {
  let inThrottle;
  return function(...args) {
    if (!inThrottle) {
      func.apply(this, args);
      inThrottle = true;
      setTimeout(() => inThrottle = false, limit);
    }
  };
}

const handleScroll = throttle(() => {
  console.log('Scroll position:', window.scrollY);
}, 200);
window.addEventListener('scroll', handleScroll);

// Use case: Countdown timer
let count = 10;
const countdown = setInterval(() => {
  console.log(count);
  count--;
  if (count < 0) {
    clearInterval(countdown);
    console.log('Done!');
  }
}, 1000);
```

**When to use:** When you need to delay execution or repeat code at intervals.

---

#### 44. **JSON.parse() / JSON.stringify()** - JSON Operations

Converts between JSON strings and JavaScript objects.

```javascript
// JSON.stringify - object to JSON string
const user = {
  name: 'John',
  age: 30,
  hobbies: ['reading', 'gaming']
};

const json = JSON.stringify(user);
console.log(json); // '{"name":"John","age":30,"hobbies":["reading","gaming"]}'

// Pretty print with indentation
const prettyJson = JSON.stringify(user, null, 2);
console.log(prettyJson);
// {
//   "name": "John",
//   "age": 30,
//   "hobbies": ["reading", "gaming"]
// }

// JSON.parse - JSON string to object
const parsed = JSON.parse(json);
console.log(parsed.name); // 'John'

// Use case: Deep clone object (simple way)
const original = { a: 1, b: { c: 2 } };
const clone = JSON.parse(JSON.stringify(original));
clone.b.c = 3;
console.log(original.b.c); // 2 (unchanged)

// ⚠️ Limitations: functions, undefined, symbols are removed
const obj = {
  name: 'John',
  greet: function() { console.log('Hi'); }, // Lost
  age: undefined, // Lost
  [Symbol('id')]: 123 // Lost
};
console.log(JSON.stringify(obj)); // '{"name":"John"}'

// Custom serialization
const obj2 = {
  name: 'John',
  password: 'secret123',
  toJSON() {
    return { name: this.name }; // Exclude password
  }
};
console.log(JSON.stringify(obj2)); // '{"name":"John"}'

// Replacer function
const data = {
  name: 'John',
  age: 30,
  password: 'secret'
};
const filtered = JSON.stringify(data, (key, value) => {
  if (key === 'password') return undefined; // Exclude password
  return value;
});

// Use case: localStorage
localStorage.setItem('user', JSON.stringify(user));
const savedUser = JSON.parse(localStorage.getItem('user'));
```

**When to use:** When you need to serialize/deserialize data for storage or transmission.

---

#### 45. **parseInt() / parseFloat()** - Parse Numbers

Converts strings to numbers.

```javascript
// parseInt - string to integer
console.log(parseInt('123'));       // 123
console.log(parseInt('123.45'));    // 123 (decimals ignored)
console.log(parseInt('   42   ')); // 42 (whitespace trimmed)

// With radix (base)
console.log(parseInt('1010', 2));   // 10 (binary)
console.log(parseInt('FF', 16));    // 255 (hexadecimal)

// ⚠️ Stops at first non-digit
console.log(parseInt('123abc'));    // 123
console.log(parseInt('abc123'));    // NaN

// parseFloat - string to decimal
console.log(parseFloat('123.45'));  // 123.45
console.log(parseFloat('3.14abc')); // 3.14

// Use case: Parse user input
const input = '  42.5  ';
const number = parseFloat(input);
if (!isNaN(number)) {
  console.log(`Valid number: ${number}`);
}

// Use case: Parse URL parameters
const url = 'example.com/page?id=123&price=45.99';
const params = new URL(url, 'http://example.com').searchParams;
const id = parseInt(params.get('id'));
const price = parseFloat(params.get('price'));

// Modern alternative: Number()
console.log(Number('123'));    // 123
console.log(Number('123.45')); // 123.45
console.log(Number('123abc')); // NaN (stricter)

// Unary plus operator
console.log(+'123');    // 123
console.log(+'123.45'); // 123.45
```

**When to use:** When converting string input to numbers.

---

#### 46. **isNaN() / Number.isNaN()** - Check for NaN

Checks if a value is NaN (Not-a-Number).

```javascript
// isNaN - converts to number first (can be misleading)
console.log(isNaN(NaN));        // true
console.log(isNaN('hello'));    // true (converted to NaN)
console.log(isNaN('123'));      // false (converted to 123)
console.log(isNaN(undefined));  // true

// Number.isNaN - strict check (recommended)
console.log(Number.isNaN(NaN));       // true
console.log(Number.isNaN('hello'));   // false (no conversion)
console.log(Number.isNaN(123));       // false
console.log(Number.isNaN(undefined)); // false

// Use case: Validate numeric input
function safeParseInt(value) {
  const num = parseInt(value);
  return Number.isNaN(num) ? null : num;
}

console.log(safeParseInt('123'));   // 123
console.log(safeParseInt('abc'));   // null

// Check if finite number
console.log(Number.isFinite(123));      // true
console.log(Number.isFinite(Infinity)); // false
console.log(Number.isFinite(NaN));      // false
console.log(Number.isFinite('123'));    // false (no conversion)

// isFinite - converts first
console.log(isFinite('123')); // true (converted to 123)
```

**When to use:** When validating numeric calculations or user input.

---

#### 47. **encodeURIComponent() / decodeURIComponent()** - URL Encoding

Encodes/decodes URL components.

```javascript
// encodeURIComponent - encode for URL
const query = 'hello world!';
const encoded = encodeURIComponent(query);
console.log(encoded); // 'hello%20world%21'

// decodeURIComponent - decode URL
const decoded = decodeURIComponent(encoded);
console.log(decoded); // 'hello world!'

// Use case: Build query string
function buildQueryString(params) {
  return Object.entries(params)
    .map(([key, value]) => 
      `${encodeURIComponent(key)}=${encodeURIComponent(value)}`
    )
    .join('&');
}

const params = {
  name: 'John Doe',
  email: 'john@example.com',
  message: 'Hello, how are you?'
};
const queryString = buildQueryString(params);
console.log(queryString);
// 'name=John%20Doe&email=john%40example.com&message=Hello%2C%20how%20are%20you%3F'

// Use case: Parse query string
function parseQueryString(query) {
  return Object.fromEntries(
    query.split('&').map(pair => {
      const [key, value] = pair.split('=');
      return [decodeURIComponent(key), decodeURIComponent(value)];
    })
  );
}

const parsed = parseQueryString(queryString);
console.log(parsed);
// { name: 'John Doe', email: 'john@example.com', message: 'Hello, how are you?' }

// encodeURI vs encodeURIComponent
const url = 'https://example.com/search?q=hello world';
console.log(encodeURI(url)); // Encodes spaces only
console.log(encodeURIComponent(url)); // Encodes all special chars
```

**When to use:** When working with URLs and query parameters.

---

#### 48. **Promise.all() / Promise.race() / Promise.allSettled()** - Promise Utilities

Combines multiple promises.

```javascript
// Promise.all - waits for all to resolve (fails if any rejects)
const promises = [
  fetch('/api/user'),
  fetch('/api/posts'),
  fetch('/api/comments')
];

Promise.all(promises)
  .then(responses => Promise.all(responses.map(r => r.json())))
  .then(([user, posts, comments]) => {
    console.log('All data loaded:', { user, posts, comments });
  })
  .catch(error => {
    console.error('One failed:', error);
  });

// Promise.race - resolves/rejects with first completed promise
const timeout = new Promise((_, reject) => 
  setTimeout(() => reject(new Error('Timeout')), 5000)
);

const fetchData = fetch('/api/data');

Promise.race([fetchData, timeout])
  .then(response => console.log('Got response'))
  .catch(error => console.error('Timeout or error:', error));

// Promise.allSettled - waits for all (doesn't fail)
const mixed = [
  Promise.resolve('Success'),
  Promise.reject('Error'),
  Promise.resolve('Another success')
];

Promise.allSettled(mixed)
  .then(results => {
    results.forEach(result => {
      if (result.status === 'fulfilled') {
        console.log('Success:', result.value);
      } else {
        console.log('Failed:', result.reason);
      }
    });
  });

// Promise.any - first successful (ignores rejections)
const servers = [
  fetch('https://server1.com/api'),
  fetch('https://server2.com/api'),
  fetch('https://server3.com/api')
];

Promise.any(servers)
  .then(response => console.log('First server responded'))
  .catch(error => console.error('All servers failed'));

// Use case: Parallel API calls with error handling
async function loadDashboard() {
  const results = await Promise.allSettled([
    fetchUser(),
    fetchStats(),
    fetchNotifications()
  ]);

  const [user, stats, notifications] = results.map(r => 
    r.status === 'fulfilled' ? r.value : null
  );

  // Continue even if some failed
  renderDashboard({ user, stats, notifications });
}
```

**When to use:** When coordinating multiple asynchronous operations.

---

#### 49. **structuredClone()** - Deep Clone

Creates a deep clone of a value.

```javascript
// Deep clone with structuredClone (modern)
const original = {
  name: 'John',
  address: {
    city: 'New York',
    zip: '10001'
  },
  hobbies: ['reading', 'gaming']
};

const clone = structuredClone(original);
clone.address.city = 'Boston';
console.log(original.address.city); // 'New York' (unchanged)

// Clones complex structures
const complex = {
  date: new Date(),
  regex: /pattern/gi,
  map: new Map([['key', 'value']]),
  set: new Set([1, 2, 3]),
  nested: { deep: { value: 123 } }
};

const cloned = structuredClone(complex);

// ⚠️ Limitations: cannot clone functions, DOM nodes, symbols
const withFunction = {
  name: 'John',
  greet() { console.log('Hi'); }
};
// structuredClone(withFunction); // Error!

// Old alternatives
// 1. JSON (limited)
const jsonClone = JSON.parse(JSON.stringify(original));

// 2. Manual deep clone
function deepClone(obj) {
  if (obj === null || typeof obj !== 'object') return obj;
  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof Array) return obj.map(item => deepClone(item));
  
  const clone = {};
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      clone[key] = deepClone(obj[key]);
    }
  }
  return clone;
}
```

**When to use:** When you need a complete deep copy of an object.

---

#### 50. **Math Functions** - Mathematical Operations

Common Math object methods.

```javascript
// Math.round, Math.ceil, Math.floor
console.log(Math.round(4.5));  // 5
console.log(Math.round(4.4));  // 4
console.log(Math.ceil(4.1));   // 5 (round up)
console.log(Math.floor(4.9));  // 4 (round down)

// Math.max, Math.min
console.log(Math.max(1, 5, 3));      // 5
console.log(Math.min(1, 5, 3));      // 1
console.log(Math.max(...[1, 5, 3])); // 5 (with spread)

// Math.random - random number between 0 and 1
console.log(Math.random()); // 0.123456...

// Random integer between min and max (inclusive)
function randomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}
console.log(randomInt(1, 10)); // 1-10

// Math.abs - absolute value
console.log(Math.abs(-5));  // 5
console.log(Math.abs(5));   // 5

// Math.pow, Math.sqrt
console.log(Math.pow(2, 3)); // 8 (2³)
console.log(2 ** 3);         // 8 (exponentiation operator)
console.log(Math.sqrt(16));  // 4

// Math.sign - sign of number
console.log(Math.sign(5));   // 1 (positive)
console.log(Math.sign(-5));  // -1 (negative)
console.log(Math.sign(0));   // 0

// Use case: Clamp value
function clamp(value, min, max) {
  return Math.min(Math.max(value, min), max);
}
console.log(clamp(150, 0, 100)); // 100

// Use case: Generate random color
function randomColor() {
  const r = randomInt(0, 255);
  const g = randomInt(0, 255);
  const b = randomInt(0, 255);
  return `rgb(${r}, ${g}, ${b})`;
}

// Use case: Round to decimal places
function roundTo(num, decimals) {
  const factor = 10 ** decimals;
  return Math.round(num * factor) / factor;
}
console.log(roundTo(3.14159, 2)); // 3.14
```

**When to use:** When performing mathematical calculations.

---

This comprehensive guide covers the most important JavaScript built-in functions and methods that every developer should master. Each function includes practical use cases and examples to help you understand when and how to use them effectively.

---

## Promise Interview Questions - Complete Guide

### Q1: What is a Promise in JavaScript? Explain its states.

**Answer:**

A **Promise** is an object representing the eventual completion or failure of an asynchronous operation. It's a proxy for a value that may not be known when the promise is created.

**Promise States:**

1. **Pending**: Initial state, neither fulfilled nor rejected
2. **Fulfilled**: Operation completed successfully
3. **Rejected**: Operation failed

```javascript
// Creating a Promise
const promise = new Promise((resolve, reject) => {
  // Asynchronous operation
  setTimeout(() => {
    const success = true;
    if (success) {
      resolve('Operation successful!'); // Fulfilled
    } else {
      reject('Operation failed!'); // Rejected
    }
  }, 1000);
});

// Consuming a Promise
promise
  .then(result => console.log(result)) // Handles fulfillment
  .catch(error => console.error(error)) // Handles rejection
  .finally(() => console.log('Cleanup')); // Always executes

// State transitions (one-way only):
// pending → fulfilled
// pending → rejected
// (Once settled, state cannot change)
```

**Key Characteristics:**

```javascript
// 1. Promises are immutable once settled
const promise = Promise.resolve('value');
promise.then(v => console.log(v)); // 'value'
promise.then(v => console.log(v)); // 'value' (same result)

// 2. Promises execute immediately
const promise2 = new Promise((resolve) => {
  console.log('Executing immediately!'); // Logs right away
  resolve('done');
});

// 3. Promises can be chained
promise2
  .then(result => result + ' 1')
  .then(result => result + ' 2')
  .then(result => console.log(result)); // 'done 1 2'
```

**Real-World Example:**

```javascript
// Fetching user data
function fetchUser(userId) {
  return new Promise((resolve, reject) => {
    fetch(`https://api.example.com/users/${userId}`)
      .then(response => {
        if (!response.ok) {
          reject(new Error('User not found'));
        }
        return response.json();
      })
      .then(data => resolve(data))
      .catch(error => reject(error));
  });
}

fetchUser(123)
  .then(user => console.log('User:', user))
  .catch(error => console.error('Error:', error));
```

---

### Q2: How do you create a Promise? Show different ways.

**Answer:**

**Method 1: Promise Constructor**

```javascript
const promise = new Promise((resolve, reject) => {
  // Async operation
  const success = Math.random() > 0.5;
  
  if (success) {
    resolve('Success!');
  } else {
    reject(new Error('Failed!'));
  }
});
```

**Method 2: Promise.resolve() - Create Fulfilled Promise**

```javascript
// Create immediately resolved promise
const resolved = Promise.resolve('Immediate value');
resolved.then(value => console.log(value)); // 'Immediate value'

// Resolves with another promise
const promise = Promise.resolve(
  new Promise(resolve => setTimeout(() => resolve('After delay'), 1000))
);

// Use case: Wrap non-promise value
function getData(useCache) {
  if (useCache) {
    return Promise.resolve(cachedData); // Wrap cached value
  } else {
    return fetch('/api/data'); // Already returns promise
  }
}
```

**Method 3: Promise.reject() - Create Rejected Promise**

```javascript
// Create immediately rejected promise
const rejected = Promise.reject(new Error('Something went wrong'));
rejected.catch(error => console.error(error));

// Use case: Early rejection
function validateInput(input) {
  if (!input) {
    return Promise.reject(new Error('Input is required'));
  }
  return processInput(input);
}
```

**Method 4: Promisify Callback-based Functions**

```javascript
// Old callback style
function readFileCallback(path, callback) {
  // ... read file
  callback(error, data);
}

// Promisified version
function readFilePromise(path) {
  return new Promise((resolve, reject) => {
    readFileCallback(path, (error, data) => {
      if (error) {
        reject(error);
      } else {
        resolve(data);
      }
    });
  });
}

// Generic promisify utility
function promisify(fn) {
  return function(...args) {
    return new Promise((resolve, reject) => {
      fn(...args, (error, result) => {
        if (error) {
          reject(error);
        } else {
          resolve(result);
        }
      });
    });
  };
}

const readFile = promisify(readFileCallback);
readFile('file.txt')
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

**Method 5: Using async Functions**

```javascript
// async function always returns a promise
async function getData() {
  return 'data'; // Automatically wrapped in Promise.resolve()
}

getData().then(data => console.log(data)); // 'data'

// Throwing in async function rejects the promise
async function fetchData() {
  throw new Error('Failed');
}

fetchData().catch(error => console.error(error)); // 'Failed'
```

**Method 6: Deferred Promise Pattern**

```javascript
// Create promise that can be resolved/rejected externally
function createDeferred() {
  let resolve, reject;
  
  const promise = new Promise((res, rej) => {
    resolve = res;
    reject = rej;
  });
  
  return { promise, resolve, reject };
}

// Usage
const { promise, resolve, reject } = createDeferred();

setTimeout(() => resolve('Done!'), 1000);

promise.then(result => console.log(result)); // 'Done!' after 1 second
```

---

### Q3: Explain Promise chaining and how to avoid callback hell.

**Answer:**

**Callback Hell (Pyramid of Doom):**

```javascript
// ❌ Callback hell - hard to read and maintain
getData(function(a) {
  getMoreData(a, function(b) {
    getMoreData(b, function(c) {
      getMoreData(c, function(d) {
        getMoreData(d, function(e) {
          console.log('Finally done!', e);
        });
      });
    });
  });
});
```

**Promise Chaining - Solution:**

```javascript
// ✅ Flat, readable promise chain
getData()
  .then(a => getMoreData(a))
  .then(b => getMoreData(b))
  .then(c => getMoreData(c))
  .then(d => getMoreData(d))
  .then(e => {
    console.log('Finally done!', e);
  })
  .catch(error => {
    console.error('Error at any step:', error);
  });
```

**How Promise Chaining Works:**

```javascript
// Each .then() returns a new promise
const promise1 = fetch('/api/user');

const promise2 = promise1.then(response => response.json());

const promise3 = promise2.then(user => {
  console.log('User:', user);
  return fetch(`/api/posts/${user.id}`); // Return another promise
});

const promise4 = promise3.then(response => response.json());

const promise5 = promise4.then(posts => {
  console.log('Posts:', posts);
});

// Compact version
fetch('/api/user')
  .then(response => response.json())
  .then(user => {
    console.log('User:', user);
    return fetch(`/api/posts/${user.id}`);
  })
  .then(response => response.json())
  .then(posts => console.log('Posts:', posts))
  .catch(error => console.error('Error:', error));
```

**Returning Values in Chains:**

```javascript
Promise.resolve(1)
  .then(value => {
    console.log(value); // 1
    return value + 1; // Return regular value
  })
  .then(value => {
    console.log(value); // 2
    return Promise.resolve(value + 1); // Return promise
  })
  .then(value => {
    console.log(value); // 3
    // Not returning anything = returns undefined
  })
  .then(value => {
    console.log(value); // undefined
  });
```

**Complex Example - User Registration Flow:**

```javascript
function registerUser(userData) {
  return validateInput(userData)
    .then(validData => {
      console.log('Data validated');
      return checkUserExists(validData.email);
    })
    .then(exists => {
      if (exists) {
        throw new Error('User already exists');
      }
      return hashPassword(userData.password);
    })
    .then(hashedPassword => {
      return createUser({
        ...userData,
        password: hashedPassword
      });
    })
    .then(user => {
      console.log('User created:', user.id);
      return sendWelcomeEmail(user.email);
    })
    .then(() => {
      return generateAuthToken(user.id);
    })
    .then(token => {
      console.log('Registration complete');
      return { user, token };
    })
    .catch(error => {
      console.error('Registration failed:', error);
      // Could add specific error handling
      if (error.message === 'User already exists') {
        // Handle duplicate user
      }
      throw error; // Re-throw if not handled
    });
}
```

**Parallel vs Sequential Chains:**

```javascript
// ❌ Sequential (slow) - waits for each to complete
function getDataSequential() {
  return fetchUser()
    .then(user => fetchPosts()) // Waits for user
    .then(posts => fetchComments()); // Waits for posts
}

// ✅ Parallel (fast) - all at once
function getDataParallel() {
  return Promise.all([
    fetchUser(),
    fetchPosts(),
    fetchComments()
  ]).then(([user, posts, comments]) => {
    return { user, posts, comments };
  });
}

// Mixed - parallel then sequential
function getDataMixed() {
  return fetchUser()
    .then(user => {
      // These can run in parallel
      return Promise.all([
        fetchPosts(user.id),
        fetchComments(user.id),
        fetchFollowers(user.id)
      ]);
    })
    .then(([posts, comments, followers]) => {
      return { posts, comments, followers };
    });
}
```

**Avoiding Common Mistakes:**

```javascript
// ❌ Breaking the chain
getData()
  .then(data => {
    processData(data); // Returns promise but not returned!
  })
  .then(result => {
    console.log(result); // undefined!
  });

// ✅ Return the promise
getData()
  .then(data => {
    return processData(data); // Must return!
  })
  .then(result => {
    console.log(result); // Correct value
  });

// ❌ Nesting promises (recreating callback hell)
getData()
  .then(data => {
    getMoreData(data).then(moreData => {
      getEvenMore(moreData).then(evenMore => {
        console.log(evenMore);
      });
    });
  });

// ✅ Flatten the chain
getData()
  .then(data => getMoreData(data))
  .then(moreData => getEvenMore(moreData))
  .then(evenMore => console.log(evenMore));
```

---

### Q4: What is the difference between Promise.all(), Promise.race(), Promise.allSettled(), and Promise.any()?

**Answer:**

**1. Promise.all() - All must succeed**

Waits for all promises to resolve. Rejects immediately if any promise rejects.

```javascript
const promise1 = Promise.resolve(1);
const promise2 = Promise.resolve(2);
const promise3 = Promise.resolve(3);

Promise.all([promise1, promise2, promise3])
  .then(values => {
    console.log(values); // [1, 2, 3]
  });

// ❌ Fails fast if any rejects
const failing = Promise.reject('Error!');
Promise.all([promise1, failing, promise3])
  .then(values => {
    console.log('Success:', values); // Never runs
  })
  .catch(error => {
    console.error('Failed:', error); // 'Error!'
  });

// Use case: Fetch multiple resources (all required)
async function loadDashboard() {
  try {
    const [user, posts, comments] = await Promise.all([
      fetch('/api/user').then(r => r.json()),
      fetch('/api/posts').then(r => r.json()),
      fetch('/api/comments').then(r => r.json())
    ]);
    
    return { user, posts, comments };
  } catch (error) {
    console.error('Failed to load dashboard:', error);
  }
}
```

**2. Promise.race() - First to settle wins**

Resolves or rejects with the first promise that settles.

```javascript
const slow = new Promise(resolve => 
  setTimeout(() => resolve('Slow'), 2000)
);
const fast = new Promise(resolve => 
  setTimeout(() => resolve('Fast'), 100)
);

Promise.race([slow, fast])
  .then(result => console.log(result)); // 'Fast'

// Use case: Timeout for fetch
function fetchWithTimeout(url, timeout = 5000) {
  const fetchPromise = fetch(url);
  const timeoutPromise = new Promise((_, reject) =>
    setTimeout(() => reject(new Error('Request timeout')), timeout)
  );
  
  return Promise.race([fetchPromise, timeoutPromise]);
}

fetchWithTimeout('/api/data', 3000)
  .then(response => response.json())
  .catch(error => console.error(error));

// Use case: Try multiple servers
const servers = [
  fetch('https://server1.com/api'),
  fetch('https://server2.com/api'),
  fetch('https://server3.com/api')
];

Promise.race(servers)
  .then(response => console.log('First server responded'))
  .catch(error => console.error('All servers slow or failed'));
```

**3. Promise.allSettled() - Wait for all, regardless of outcome**

Waits for all promises to settle (either resolve or reject). Never rejects.

```javascript
const promises = [
  Promise.resolve('Success 1'),
  Promise.reject('Error 1'),
  Promise.resolve('Success 2'),
  Promise.reject('Error 2')
];

Promise.allSettled(promises)
  .then(results => {
    results.forEach((result, index) => {
      if (result.status === 'fulfilled') {
        console.log(`Promise ${index} succeeded:`, result.value);
      } else {
        console.log(`Promise ${index} failed:`, result.reason);
      }
    });
  });

// Output:
// Promise 0 succeeded: Success 1
// Promise 1 failed: Error 1
// Promise 2 succeeded: Success 2
// Promise 3 failed: Error 2

// Use case: Run multiple independent operations
async function runHealthChecks() {
  const checks = [
    checkDatabase(),
    checkCache(),
    checkExternalAPI(),
    checkFileSystem()
  ];
  
  const results = await Promise.allSettled(checks);
  
  const report = results.map((result, index) => ({
    check: ['Database', 'Cache', 'API', 'FileSystem'][index],
    status: result.status === 'fulfilled' ? 'healthy' : 'unhealthy',
    details: result.status === 'fulfilled' ? result.value : result.reason
  }));
  
  return report;
}

// Use case: Batch operations with error tracking
async function processItems(items) {
  const results = await Promise.allSettled(
    items.map(item => processItem(item))
  );
  
  const successful = results.filter(r => r.status === 'fulfilled');
  const failed = results.filter(r => r.status === 'rejected');
  
  console.log(`Processed ${successful.length} items`);
  console.log(`Failed ${failed.length} items`);
  
  return {
    successful: successful.map(r => r.value),
    failed: failed.map(r => r.reason)
  };
}
```

**4. Promise.any() - First successful promise**

Resolves with the first fulfilled promise. Rejects only if all promises reject.

```javascript
const promises = [
  Promise.reject('Error 1'),
  Promise.resolve('Success 1'),
  Promise.resolve('Success 2')
];

Promise.any(promises)
  .then(result => console.log(result)) // 'Success 1'
  .catch(error => console.error(error));

// Rejects only if all fail
const allFail = [
  Promise.reject('Error 1'),
  Promise.reject('Error 2'),
  Promise.reject('Error 3')
];

Promise.any(allFail)
  .then(result => console.log(result))
  .catch(error => {
    console.error('All failed:', error); // AggregateError
    console.error('Individual errors:', error.errors);
  });

// Use case: Redundant resource fetching
async function fetchFromMirrors(resource) {
  const mirrors = [
    fetch(`https://mirror1.com/${resource}`),
    fetch(`https://mirror2.com/${resource}`),
    fetch(`https://mirror3.com/${resource}`)
  ];
  
  try {
    const response = await Promise.any(mirrors);
    return await response.json();
  } catch (error) {
    console.error('All mirrors failed:', error);
    throw new Error('Resource unavailable');
  }
}

// Use case: Try multiple authentication methods
async function authenticate(credentials) {
  const methods = [
    authenticateWithOAuth(credentials),
    authenticateWithSAML(credentials),
    authenticateWithLDAP(credentials)
  ];
  
  try {
    const result = await Promise.any(methods);
    console.log('Authenticated successfully');
    return result;
  } catch (error) {
    console.error('All authentication methods failed');
    throw new Error('Authentication failed');
  }
}
```

**Comparison Table:**

| Method | Resolves When | Rejects When | Use Case |
|--------|---------------|--------------|----------|
| **Promise.all()** | All fulfill | Any rejects | All operations required |
| **Promise.race()** | First settles | First settles with rejection | Timeout, fastest response |
| **Promise.allSettled()** | All settle | Never | Independent operations, need all results |
| **Promise.any()** | First fulfills | All reject | Redundancy, first success wins |

**Visual Example:**

```javascript
async function demonstrateAll() {
  const p1 = delay(1000, 'one');
  const p2 = delay(2000, 'two');
  const p3 = delay(3000, 'three');
  
  console.log('Starting...');
  
  // Promise.all - waits for all (3 seconds)
  const allResult = await Promise.all([p1, p2, p3]);
  console.log('All:', allResult); // ['one', 'two', 'three']
  
  // Promise.race - first to finish (1 second)
  const raceResult = await Promise.race([p1, p2, p3]);
  console.log('Race:', raceResult); // 'one'
  
  // Promise.any - first successful (1 second)
  const anyResult = await Promise.any([p1, p2, p3]);
  console.log('Any:', anyResult); // 'one'
  
  // Promise.allSettled - waits for all (3 seconds)
  const settledResult = await Promise.allSettled([p1, p2, p3]);
  console.log('AllSettled:', settledResult);
  // [
  //   { status: 'fulfilled', value: 'one' },
  //   { status: 'fulfilled', value: 'two' },
  //   { status: 'fulfilled', value: 'three' }
  // ]
}

function delay(ms, value) {
  return new Promise(resolve => setTimeout(() => resolve(value), ms));
}
```

---

### Q5: What is async/await? How is it different from Promises?

**Answer:**

**async/await** is syntactic sugar built on top of Promises that makes asynchronous code look and behave more like synchronous code.

**Basic Syntax:**

```javascript
// Promise-based approach
function fetchData() {
  return fetch('/api/data')
    .then(response => response.json())
    .then(data => {
      console.log('Data:', data);
      return data;
    })
    .catch(error => {
      console.error('Error:', error);
      throw error;
    });
}

// async/await approach
async function fetchData() {
  try {
    const response = await fetch('/api/data');
    const data = await response.json();
    console.log('Data:', data);
    return data;
  } catch (error) {
    console.error('Error:', error);
    throw error;
  }
}
```

**How async/await Works:**

```javascript
// 1. async function always returns a Promise
async function getValue() {
  return 42; // Automatically wrapped in Promise.resolve(42)
}

getValue().then(value => console.log(value)); // 42

// 2. await pauses execution until promise resolves
async function example() {
  console.log('Start');
  
  const result = await Promise.resolve('Done');
  // Execution pauses here until promise resolves
  
  console.log('Result:', result);
  console.log('End');
}
// Output:
// Start
// Result: Done
// End

// 3. Error handling with try/catch
async function handleErrors() {
  try {
    const data = await fetch('/api/data');
    return data;
  } catch (error) {
    console.error('Caught:', error);
    return null;
  }
}
```

**Complex Example - Sequential vs Parallel:**

```javascript
// Sequential execution (slow)
async function sequential() {
  const user = await fetchUser(); // Wait 1s
  const posts = await fetchPosts(); // Wait 1s
  const comments = await fetchComments(); // Wait 1s
  return { user, posts, comments };
  // Total: 3 seconds
}

// Parallel execution (fast)
async function parallel() {
  const [user, posts, comments] = await Promise.all([
    fetchUser(),
    fetchPosts(),
    fetchComments()
  ]);
  return { user, posts, comments };
  // Total: 1 second (all run simultaneously)
}

// Mixed approach
async function mixed() {
  const user = await fetchUser(); // Must happen first
  
  // These can run in parallel after we have user
  const [posts, comments] = await Promise.all([
    fetchPosts(user.id),
    fetchComments(user.id)
  ]);
  
  return { user, posts, comments };
}
```

**Real-World Example:**

```javascript
// User registration with async/await
async function registerUser(userData) {
  try {
    // Validate input
    const validData = await validateInput(userData);
    console.log('Validation passed');
    
    // Check if user exists
    const exists = await checkUserExists(validData.email);
    if (exists) {
      throw new Error('User already exists');
    }
    
    // Hash password
    const hashedPassword = await hashPassword(validData.password);
    
    // Create user in database
    const user = await createUser({
      ...validData,
      password: hashedPassword
    });
    console.log('User created:', user.id);
    
    // Send welcome email (don't wait for it)
    sendWelcomeEmail(user.email).catch(err => 
      console.error('Email failed:', err)
    );
    
    // Generate auth token
    const token = await generateAuthToken(user.id);
    
    return { user, token };
    
  } catch (error) {
    console.error('Registration failed:', error);
    
    // Handle specific errors
    if (error.message === 'User already exists') {
      throw new Error('Email already registered');
    }
    
    throw error;
  }
}
```

**Error Handling Patterns:**

```javascript
// Pattern 1: Try/catch per operation
async function pattern1() {
  let user;
  try {
    user = await fetchUser();
  } catch (error) {
    console.error('Failed to fetch user:', error);
    return null;
  }
  
  let posts;
  try {
    posts = await fetchPosts(user.id);
  } catch (error) {
    console.error('Failed to fetch posts:', error);
    posts = [];
  }
  
  return { user, posts };
}

// Pattern 2: Single try/catch
async function pattern2() {
  try {
    const user = await fetchUser();
    const posts = await fetchPosts(user.id);
    return { user, posts };
  } catch (error) {
    console.error('Operation failed:', error);
    throw error;
  }
}

// Pattern 3: Fallback values
async function pattern3() {
  const user = await fetchUser().catch(err => {
    console.error('User fetch failed:', err);
    return { id: null, name: 'Guest' };
  });
  
  const posts = await fetchPosts(user.id).catch(() => []);
  
  return { user, posts };
}

// Pattern 4: Helper function
async function safeAwait(promise) {
  try {
    const data = await promise;
    return [null, data];
  } catch (error) {
    return [error, null];
  }
}

async function pattern4() {
  const [userError, user] = await safeAwait(fetchUser());
  if (userError) {
    console.error('User fetch failed:', userError);
    return null;
  }
  
  const [postsError, posts] = await safeAwait(fetchPosts(user.id));
  if (postsError) {
    console.error('Posts fetch failed:', postsError);
  }
  
  return { user, posts: posts || [] };
}
```

**async/await vs Promises Comparison:**

```javascript
// Promises - more verbose for complex flows
function promiseVersion() {
  return fetchUser()
    .then(user => {
      return fetchPosts(user.id)
        .then(posts => {
          return fetchComments(posts[0].id)
            .then(comments => {
              return { user, posts, comments };
            });
        });
    })
    .catch(error => {
      console.error('Error:', error);
      throw error;
    });
}

// async/await - cleaner, easier to read
async function asyncVersion() {
  try {
    const user = await fetchUser();
    const posts = await fetchPosts(user.id);
    const comments = await fetchComments(posts[0].id);
    return { user, posts, comments };
  } catch (error) {
    console.error('Error:', error);
    throw error;
  }
}
```

**Await in Loops:**

```javascript
// ❌ Sequential (slow) - each awaits previous
async function processSequential(items) {
  const results = [];
  for (const item of items) {
    const result = await processItem(item); // Waits for each
    results.push(result);
  }
  return results;
}

// ✅ Parallel (fast) - all at once
async function processParallel(items) {
  const promises = items.map(item => processItem(item));
  return await Promise.all(promises);
}

// Mixed - process in batches
async function processBatches(items, batchSize = 5) {
  const results = [];
  for (let i = 0; i < items.length; i += batchSize) {
    const batch = items.slice(i, i + batchSize);
    const batchResults = await Promise.all(
      batch.map(item => processItem(item))
    );
    results.push(...batchResults);
  }
  return results;
}
```

**Top-Level await (Modern JavaScript):**

```javascript
// In ES modules, can use await at top level
// main.js
const data = await fetch('/api/data').then(r => r.json());
console.log('Data loaded:', data);

export default data;

// Before, had to wrap in async function
(async () => {
  const data = await fetch('/api/data').then(r => r.json());
  console.log('Data loaded:', data);
})();
```

**When to Use async/await vs Promises:**

| Scenario | Recommendation |
|----------|----------------|
| Sequential operations | async/await (cleaner) |
| Parallel operations | Promise.all with await |
| Error handling | async/await with try/catch |
| Simple chains | Either works, async/await cleaner |
| Library/framework code | Promises (more explicit) |
| Need fine control | Promises |
| Complex branching logic | async/await (easier to read) |

---

### Q6: How do you handle errors in Promises and async/await?

**Answer:**

**Promise Error Handling:**

```javascript
// Method 1: .catch() at the end
fetch('/api/data')
  .then(response => response.json())
  .then(data => processData(data))
  .then(result => console.log(result))
  .catch(error => {
    console.error('Error anywhere in chain:', error);
  });

// Method 2: .catch() for specific step
fetch('/api/data')
  .then(response => response.json())
  .catch(error => {
    console.error('Fetch or parse failed:', error);
    return { default: 'data' }; // Provide fallback
  })
  .then(data => processData(data))
  .catch(error => {
    console.error('Processing failed:', error);
  });

// Method 3: Error recovery in chain
fetch('/api/data')
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }
    return response.json();
  })
  .then(data => processData(data))
  .catch(error => {
    console.error('Primary failed:', error);
    return fetch('/api/backup-data'); // Try backup
  })
  .then(response => response.json())
  .catch(error => {
    console.error('Both failed:', error);
    return { fallback: true }; // Ultimate fallback
  });

// Method 4: Finally for cleanup
let loading = true;
fetch('/api/data')
  .then(response => response.json())
  .then(data => updateUI(data))
  .catch(error => showError(error))
  .finally(() => {
    loading = false; // Always runs
    hideSpinner();
  });
```

**async/await Error Handling:**

```javascript
// Method 1: try/catch block
async function fetchData() {
  try {
    const response = await fetch('/api/data');
    
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    }
    
    const data = await response.json();
    const processed = await processData(data);
    
    return processed;
    
  } catch (error) {
    console.error('Error:', error.message);
    throw error; // Re-throw or handle
  }
}

// Method 2: Multiple try/catch blocks
async function complexOperation() {
  let user;
  
  try {
    user = await fetchUser();
  } catch (error) {
    console.error('User fetch failed:', error);
    user = getDefaultUser();
  }
  
  let posts;
  try {
    posts = await fetchPosts(user.id);
  } catch (error) {
    console.error('Posts fetch failed:', error);
    posts = [];
  }
  
  return { user, posts };
}

// Method 3: Try/catch with finally
async function withCleanup() {
  let connection;
  
  try {
    connection = await openConnection();
    const data = await connection.query('SELECT * FROM users');
    return data;
    
  } catch (error) {
    console.error('Database error:', error);
    throw error;
    
  } finally {
    if (connection) {
      await connection.close(); // Always cleanup
    }
  }
}

// Method 4: Catch on await
async function selectiveCatch() {
  const user = await fetchUser().catch(error => {
    console.error('User failed:', error);
    return { id: null, name: 'Guest' };
  });
  
  const posts = await fetchPosts(user.id).catch(() => []);
  
  return { user, posts };
}
```

**Custom Error Classes:**

```javascript
class APIError extends Error {
  constructor(message, statusCode, response) {
    super(message);
    this.name = 'APIError';
    this.statusCode = statusCode;
    this.response = response;
  }
}

class ValidationError extends Error {
  constructor(message, field) {
    super(message);
    this.name = 'ValidationError';
    this.field = field;
  }
}

async function fetchUser(id) {
  try {
    const response = await fetch(`/api/users/${id}`);
    
    if (!response.ok) {
      const errorData = await response.json();
      throw new APIError(
        errorData.message || 'API request failed',
        response.status,
        errorData
      );
    }
    
    const user = await response.json();
    
    if (!user.email) {
      throw new ValidationError('Email is required', 'email');
    }
    
    return user;
    
  } catch (error) {
    if (error instanceof APIError) {
      console.error(`API Error ${error.statusCode}:`, error.message);
      if (error.statusCode === 404) {
        return null; // User not found
      }
    } else if (error instanceof ValidationError) {
      console.error(`Validation failed for ${error.field}:`, error.message);
    } else {
      console.error('Unexpected error:', error);
    }
    
    throw error;
  }
}
```

**Error Handling Utilities:**

```javascript
// Safe async wrapper
function safeAsync(fn) {
  return async function(...args) {
    try {
      return await fn(...args);
    } catch (error) {
      console.error(`Error in ${fn.name}:`, error);
      return null;
    }
  };
}

const safeFetchUser = safeAsync(async (id) => {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
});

// Result wrapper (Go-style)
async function to(promise) {
  try {
    const data = await promise;
    return [null, data];
  } catch (error) {
    return [error, null];
  }
}

async function example() {
  const [userError, user] = await to(fetchUser(123));
  
  if (userError) {
    console.error('Failed to fetch user:', userError);
    return;
  }
  
  const [postsError, posts] = await to(fetchPosts(user.id));
  
  if (postsError) {
    console.error('Failed to fetch posts:', postsError);
  }
  
  return { user, posts: posts || [] };
}

// Retry utility
async function retry(fn, maxAttempts = 3, delay = 1000) {
  for (let attempt = 1; attempt <= maxAttempts; attempt++) {
    try {
      return await fn();
    } catch (error) {
      if (attempt === maxAttempts) {
        throw error;
      }
      
      console.log(`Attempt ${attempt} failed, retrying in ${delay}ms...`);
      await new Promise(resolve => setTimeout(resolve, delay));
      delay *= 2; // Exponential backoff
    }
  }
}

// Usage
const data = await retry(() => fetch('/api/data').then(r => r.json()));

// Timeout utility
async function withTimeout(promise, ms) {
  const timeout = new Promise((_, reject) =>
    setTimeout(() => reject(new Error('Operation timed out')), ms)
  );
  
  return Promise.race([promise, timeout]);
}

// Usage
try {
  const data = await withTimeout(fetchData(), 5000);
  console.log('Data:', data);
} catch (error) {
  if (error.message === 'Operation timed out') {
    console.error('Request took too long');
  }
}
```

**Global Error Handlers:**

```javascript
// Unhandled promise rejections
window.addEventListener('unhandledrejection', event => {
  console.error('Unhandled rejection:', event.reason);
  
  // Prevent default browser behavior
  event.preventDefault();
  
  // Log to error tracking service
  logToSentry(event.reason);
  
  // Show user-friendly message
  showNotification('Something went wrong. Please try again.');
});

// Example of unhandled rejection
async function buggyFunction() {
  throw new Error('This will be unhandled!');
}

buggyFunction(); // No .catch() - triggers unhandledrejection

// Proper handling
buggyFunction().catch(error => console.error('Caught:', error));
```

**Best Practices:**

```javascript
// ✅ Always handle errors
async function good() {
  try {
    return await fetchData();
  } catch (error) {
    console.error('Error:', error);
    return defaultData;
  }
}

// ❌ Silent failures
async function bad() {
  return await fetchData(); // Error bubbles up uncaught
}

// ✅ Specific error handling
async function specificHandling() {
  try {
    const data = await fetchData();
    return processData(data);
  } catch (error) {
    if (error.name === 'NetworkError') {
      return retryWithBackup();
    } else if (error.name === 'ValidationError') {
      throw new UserFacingError('Invalid data received');
    } else {
      logError(error);
      throw error;
    }
  }
}

// ✅ Cleanup with finally
async function withCleanup() {
  const resource = await acquireResource();
  
  try {
    return await useResource(resource);
  } catch (error) {
    console.error('Resource usage failed:', error);
    throw error;
  } finally {
    await releaseResource(resource); // Always cleanup
  }
}

// ✅ Parallel error handling
async function parallelErrors() {
  const results = await Promise.allSettled([
    fetchUser(),
    fetchPosts(),
    fetchComments()
  ]);
  
  const errors = results
    .filter(r => r.status === 'rejected')
    .map(r => r.reason);
  
  if (errors.length > 0) {
    console.error('Some operations failed:', errors);
  }
  
  const data = results
    .filter(r => r.status === 'fulfilled')
    .map(r => r.value);
  
  return data;
}
```

---

### Q7: Explain Promise composition patterns and advanced techniques.

**Answer:**

**Pattern 1: Promise Pipelining**

```javascript
// Chain of transformations
const pipeline = (value) =>
  Promise.resolve(value)
    .then(v => v * 2)
    .then(v => v + 10)
    .then(v => v.toString())
    .then(v => `Result: ${v}`);

pipeline(5).then(console.log); // 'Result: 20'

// Generic pipeline function
function pipe(...fns) {
  return (value) => {
    return fns.reduce(
      (promise, fn) => promise.then(fn),
      Promise.resolve(value)
    );
  };
}

const transform = pipe(
  (x) => x * 2,
  (x) => x + 10,
  (x) => x.toString(),
  (x) => `Result: ${x}`
);

transform(5).then(console.log); // 'Result: 20'

// Async pipeline
function asyncPipe(...fns) {
  return async (value) => {
    let result = value;
    for (const fn of fns) {
      result = await fn(result);
    }
    return result;
  };
}

const asyncTransform = asyncPipe(
  async (x) => x * 2,
  async (x) => await delay(100, x + 10),
  async (x) => x.toString()
);
```

**Pattern 2: Sequential Execution**

```javascript
// Execute promises one after another
async function sequential(tasks) {
  const results = [];
  for (const task of tasks) {
    const result = await task();
    results.push(result);
  }
  return results;
}

const tasks = [
  () => fetchUser(1),
  () => fetchUser(2),
  () => fetchUser(3)
];

const users = await sequential(tasks);

// With reduce
function sequentialReduce(tasks) {
  return tasks.reduce(
    (promise, task) => promise.then(results =>
      task().then(result => [...results, result])
    ),
    Promise.resolve([])
  );
}
```

**Pattern 3: Parallel Batching**

```javascript
// Process items in parallel batches
async function batchProcess(items, batchSize, processFn) {
  const results = [];
  
  for (let i = 0; i < items.length; i += batchSize) {
    const batch = items.slice(i, i + batchSize);
    const batchResults = await Promise.all(
      batch.map(item => processFn(item))
    );
    results.push(...batchResults);
    
    // Optional: delay between batches
    if (i + batchSize < items.length) {
      await delay(100);
    }
  }
  
  return results;
}

// Usage
const userIds = Array.from({ length: 100 }, (_, i) => i + 1);
const users = await batchProcess(userIds, 10, fetchUser);
// Fetches in batches of 10

// With concurrency limit
async function limitConcurrency(tasks, limit) {
  const results = [];
  const executing = [];
  
  for (const task of tasks) {
    const promise = task().then(result => {
      executing.splice(executing.indexOf(promise), 1);
      return result;
    });
    
    results.push(promise);
    executing.push(promise);
    
    if (executing.length >= limit) {
      await Promise.race(executing);
    }
  }
  
  return Promise.all(results);
}

// Process max 5 at a time
const limited = await limitConcurrency(tasks, 5);
```

**Pattern 4: Promise Memoization**

```javascript
// Cache promise results
function memoizeAsync(fn) {
  const cache = new Map();
  
  return function(...args) {
    const key = JSON.stringify(args);
    
    if (cache.has(key)) {
      return cache.get(key);
    }
    
    const promise = fn(...args).catch(error => {
      cache.delete(key); // Remove from cache on error
      throw error;
    });
    
    cache.set(key, promise);
    return promise;
  };
}

const memoizedFetch = memoizeAsync(async (url) => {
  const response = await fetch(url);
  return response.json();
});

// First call - fetches from server
await memoizedFetch('/api/user/1');

// Second call - returns cached promise
await memoizedFetch('/api/user/1'); // Instant!

// With expiration
function memoizeAsyncWithTTL(fn, ttl = 60000) {
  const cache = new Map();
  
  return function(...args) {
    const key = JSON.stringify(args);
    const cached = cache.get(key);
    
    if (cached && Date.now() - cached.timestamp < ttl) {
      return cached.promise;
    }
    
    const promise = fn(...args);
    cache.set(key, {
      promise,
      timestamp: Date.now()
    });
    
    return promise;
  };
}
```

**Pattern 5: Retry with Exponential Backoff**

```javascript
async function retryWithBackoff(
  fn,
  maxRetries = 3,
  baseDelay = 1000,
  maxDelay = 30000
) {
  let lastError;
  
  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      return await fn();
    } catch (error) {
      lastError = error;
      
      if (attempt < maxRetries - 1) {
        const delay = Math.min(
          baseDelay * Math.pow(2, attempt),
          maxDelay
        );
        
        console.log(`Retry ${attempt + 1}/${maxRetries} in ${delay}ms`);
        await new Promise(resolve => setTimeout(resolve, delay));
      }
    }
  }
  
  throw lastError;
}

// Usage
const data = await retryWithBackoff(
  () => fetch('/api/data').then(r => r.json()),
  5,
  1000
);

// With jitter (randomness)
async function retryWithJitter(fn, maxRetries = 3) {
  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      return await fn();
    } catch (error) {
      if (attempt < maxRetries - 1) {
        const baseDelay = 1000 * Math.pow(2, attempt);
        const jitter = Math.random() * 1000;
        const delay = baseDelay + jitter;
        
        await new Promise(resolve => setTimeout(resolve, delay));
      } else {
        throw error;
      }
    }
  }
}
```

**Pattern 6: Circuit Breaker**

```javascript
class CircuitBreaker {
  constructor(fn, options = {}) {
    this.fn = fn;
    this.failureThreshold = options.failureThreshold || 5;
    this.resetTimeout = options.resetTimeout || 60000;
    this.state = 'CLOSED'; // CLOSED, OPEN, HALF_OPEN
    this.failureCount = 0;
    this.nextAttempt = Date.now();
  }
  
  async execute(...args) {
    if (this.state === 'OPEN') {
      if (Date.now() < this.nextAttempt) {
        throw new Error('Circuit breaker is OPEN');
      }
      this.state = 'HALF_OPEN';
    }
    
    try {
      const result = await this.fn(...args);
      this.onSuccess();
      return result;
    } catch (error) {
      this.onFailure();
      throw error;
    }
  }
  
  onSuccess() {
    this.failureCount = 0;
    this.state = 'CLOSED';
  }
  
  onFailure() {
    this.failureCount++;
    
    if (this.failureCount >= this.failureThreshold) {
      this.state = 'OPEN';
      this.nextAttempt = Date.now() + this.resetTimeout;
      console.log('Circuit breaker opened');
    }
  }
}

// Usage
const breaker = new CircuitBreaker(
  (url) => fetch(url).then(r => r.json()),
  { failureThreshold: 3, resetTimeout: 30000 }
);

try {
  const data = await breaker.execute('/api/data');
} catch (error) {
  console.error('Request failed or circuit open');
}
```

**Pattern 7: Promise Pool**

```javascript
class PromisePool {
  constructor(maxConcurrent = 5) {
    this.maxConcurrent = maxConcurrent;
    this.currentlyRunning = 0;
    this.queue = [];
  }
  
  async add(promiseFn) {
    while (this.currentlyRunning >= this.maxConcurrent) {
      await Promise.race(this.queue);
    }
    
    this.currentlyRunning++;
    
    const promise = promiseFn()
      .then(result => {
        this.currentlyRunning--;
        this.queue.splice(this.queue.indexOf(promise), 1);
        return result;
      })
      .catch(error => {
        this.currentlyRunning--;
        this.queue.splice(this.queue.indexOf(promise), 1);
        throw error;
      });
    
    this.queue.push(promise);
    return promise;
  }
  
  async all(promiseFns) {
    return Promise.all(
      promiseFns.map(fn => this.add(fn))
    );
  }
}

// Usage
const pool = new PromisePool(3);

const tasks = Array.from({ length: 10 }, (_, i) => 
  () => fetchUser(i + 1)
);

const results = await pool.all(tasks);
// Only 3 running at a time
```

**Pattern 8: Debounced Promises**

```javascript
function debounceAsync(fn, delay) {
  let timeoutId;
  let latestResolve;
  let latestReject;
  
  return function(...args) {
    return new Promise((resolve, reject) => {
      clearTimeout(timeoutId);
      
      latestResolve = resolve;
      latestReject = reject;
      
      timeoutId = setTimeout(async () => {
        try {
          const result = await fn(...args);
          latestResolve(result);
        } catch (error) {
          latestReject(error);
        }
      }, delay);
    });
  };
}

// Usage
const debouncedSearch = debounceAsync(
  async (query) => {
    const response = await fetch(`/api/search?q=${query}`);
    return response.json();
  },
  500
);

// Multiple rapid calls
debouncedSearch('jav'); // Cancelled
debouncedSearch('java'); // Cancelled
debouncedSearch('javasc'); // Cancelled
debouncedSearch('javascript'); // Executes after 500ms
```

**Pattern 9: Promise Cancellation (AbortController)**

```javascript
// Cancellable fetch
async function cancellableFetch(url, options = {}) {
  const controller = new AbortController();
  const { signal } = controller;
  
  const fetchPromise = fetch(url, { ...options, signal })
    .then(response => response.json());
  
  return {
    promise: fetchPromise,
    cancel: () => controller.abort()
  };
}

// Usage
const { promise, cancel } = cancellableFetch('/api/data');

// Cancel after 5 seconds
setTimeout(cancel, 5000);

try {
  const data = await promise;
  console.log('Data:', data);
} catch (error) {
  if (error.name === 'AbortError') {
    console.log('Request was cancelled');
  }
}

// Generic cancellable promise
function makeCancellable(promise) {
  let hasCancelled = false;
  
  const wrappedPromise = new Promise((resolve, reject) => {
    promise
      .then(value => hasCancelled ? reject({ isCancelled: true }) : resolve(value))
      .catch(error => hasCancelled ? reject({ isCancelled: true }) : reject(error));
  });
  
  return {
    promise: wrappedPromise,
    cancel: () => { hasCancelled = true; }
  };
}
```

**Pattern 10: Lazy Promise**

```javascript
// Promise that doesn't execute until .then() is called
class LazyPromise {
  constructor(executor) {
    this.executor = executor;
    this.promise = null;
  }
  
  then(onFulfilled, onRejected) {
    if (!this.promise) {
      this.promise = new Promise(this.executor);
    }
    return this.promise.then(onFulfilled, onRejected);
  }
  
  catch(onRejected) {
    return this.then(null, onRejected);
  }
}

// Usage
const lazy = new LazyPromise((resolve) => {
  console.log('Executing now!'); // Only when .then() is called
  setTimeout(() => resolve('Done'), 1000);
});

// Not executed yet...
await delay(2000);

// Now it executes
lazy.then(result => console.log(result));
```

These patterns enable sophisticated asynchronous flow control and are commonly used in production applications for handling complex scenarios like rate limiting, error recovery, and resource management.

---

### Q8: What are common Promise anti-patterns and mistakes?

**Answer:**

**Anti-Pattern 1: The Nested Promise Hell**

```javascript
// ❌ Nesting promises (recreates callback hell)
fetchUser()
  .then(user => {
    fetchPosts(user.id).then(posts => {
      fetchComments(posts[0].id).then(comments => {
        console.log({ user, posts, comments });
      });
    });
  });

// ✅ Flat promise chain
fetchUser()
  .then(user => {
    this.user = user;
    return fetchPosts(user.id);
  })
  .then(posts => {
    this.posts = posts;
    return fetchComments(posts[0].id);
  })
  .then(comments => {
    console.log({ user: this.user, posts: this.posts, comments });
  });

// ✅ Better with async/await
async function getData() {
  const user = await fetchUser();
  const posts = await fetchPosts(user.id);
  const comments = await fetchComments(posts[0].id);
  return { user, posts, comments };
}
```

**Anti-Pattern 2: Forgetting to Return**

```javascript
// ❌ Not returning promise
function getUserData(id) {
  fetchUser(id)
    .then(user => {
      return user.data; // This return is for .then(), not getUserData()
    });
}

const data = getUserData(123); // undefined!

// ✅ Return the promise
function getUserData(id) {
  return fetchUser(id)
    .then(user => user.data);
}

const data = await getUserData(123); // Correct!

// ❌ Breaking the chain
getData()
  .then(data => {
    processData(data); // Returns promise but not returned!
  })
  .then(result => {
    console.log(result); // undefined
  });

// ✅ Return the promise
getData()
  .then(data => {
    return processData(data);
  })
  .then(result => {
    console.log(result); // Correct value
  });
```

**Anti-Pattern 3: Using async Without await**

```javascript
// ❌ Unnecessary async
async function getUser(id) {
  return fetchUser(id); // Don't need async here
}

// ✅ Remove async if not awaiting
function getUser(id) {
  return fetchUser(id);
}

// ❌ async function not awaited
async function process() {
  const result = fetchData(); // Missing await!
  console.log(result); // Promise object, not the data
}

// ✅ await the async function
async function process() {
  const result = await fetchData();
  console.log(result); // Actual data
}
```

**Anti-Pattern 4: Not Handling Errors**

```javascript
// ❌ No error handling
async function getData() {
  const data = await fetch('/api/data');
  return data.json();
  // If fetch fails, error bubbles up unhandled
}

getData(); // Unhandled promise rejection!

// ✅ Proper error handling
async function getData() {
  try {
    const data = await fetch('/api/data');
    return data.json();
  } catch (error) {
    console.error('Failed to fetch data:', error);
    return null;
  }
}

// ❌ Silent failures
fetchData().catch(() => {}); // Error swallowed!

// ✅ At least log the error
fetchData().catch(error => {
  console.error('Error:', error);
  // Handle or re-throw
});
```

**Anti-Pattern 5: Creating Unnecessary Promises**

```javascript
// ❌ Wrapping already-promised values
async function getUser(id) {
  return new Promise((resolve) => {
    fetchUser(id).then(user => {
      resolve(user); // Unnecessary wrapping
    });
  });
}

// ✅ Just return the existing promise
async function getUser(id) {
  return fetchUser(id);
}

// or even simpler
function getUser(id) {
  return fetchUser(id);
}

// ❌ Promise constructor for async function
function getData() {
  return new Promise(async (resolve, reject) => {
    try {
      const data = await fetchData();
      resolve(data);
    } catch (error) {
      reject(error);
    }
  });
}

// ✅ Just use async function
async function getData() {
  return await fetchData();
}
```

**Anti-Pattern 6: Sequential When Could Be Parallel**

```javascript
// ❌ Sequential (slow)
async function loadData() {
  const users = await fetchUsers(); // 1 second
  const posts = await fetchPosts(); // 1 second
  const comments = await fetchComments(); // 1 second
  return { users, posts, comments };
  // Total: 3 seconds
}

// ✅ Parallel (fast)
async function loadData() {
  const [users, posts, comments] = await Promise.all([
    fetchUsers(),
    fetchPosts(),
    fetchComments()
  ]);
  return { users, posts, comments };
  // Total: 1 second (all run together)
}

// ❌ Using await in map
async function processItems(items) {
  return items.map(async item => {
    return await processItem(item);
  });
  // Returns array of promises!
}

// ✅ Properly await Promise.all
async function processItems(items) {
  return await Promise.all(
    items.map(item => processItem(item))
  );
}
```

**Anti-Pattern 7: Using .then() with async/await**

```javascript
// ❌ Mixing paradigms
async function getData() {
  return await fetchData()
    .then(data => processData(data))
    .then(result => result.value);
}

// ✅ Stick with async/await
async function getData() {
  const data = await fetchData();
  const processed = await processData(data);
  return processed.value;
}

// or stick with promises
function getData() {
  return fetchData()
    .then(data => processData(data))
    .then(result => result.value);
}
```

**Anti-Pattern 8: Ignoring Promise.all() Failures**

```javascript
// ❌ All fail if one fails
async function loadAll() {
  try {
    const [users, posts, comments] = await Promise.all([
      fetchUsers(),
      fetchPosts(), // If this fails, you get nothing
      fetchComments()
    ]);
  } catch (error) {
    // One failure = lose all results
  }
}

// ✅ Use Promise.allSettled for independent operations
async function loadAll() {
  const results = await Promise.allSettled([
    fetchUsers(),
    fetchPosts(),
    fetchComments()
  ]);
  
  const [users, posts, comments] = results.map(result =>
    result.status === 'fulfilled' ? result.value : null
  );
  
  return { users, posts, comments };
  // Get what succeeded, null for failures
}
```

**Anti-Pattern 9: Modifying Promise Prototype**

```javascript
// ❌ Never modify built-in prototypes
Promise.prototype.delay = function(ms) {
  return this.then(value =>
    new Promise(resolve => setTimeout(() => resolve(value), ms))
  );
};

// ✅ Use utility functions
function delay(promise, ms) {
  return promise.then(value =>
    new Promise(resolve => setTimeout(() => resolve(value), ms))
  );
}

// or helper function
const delayedFetch = (url) =>
  fetch(url).then(r => 
    new Promise(resolve => 
      setTimeout(() => resolve(r.json()), 1000)
    )
  );
```

**Anti-Pattern 10: Race Conditions**

```javascript
// ❌ Race condition
let data;

async function getData() {
  if (!data) {
    data = await fetchData(); // Multiple calls = multiple fetches!
  }
  return data;
}

// Called twice rapidly
getData();
getData();
// Both calls fetch because data is still undefined

// ✅ Cache the promise itself
let dataPromise;

async function getData() {
  if (!dataPromise) {
    dataPromise = fetchData();
  }
  return dataPromise;
}

// Both calls share same promise
getData();
getData();
```

**Anti-Pattern 11: forEach with async**

```javascript
// ❌ forEach doesn't wait for async
async function processAll(items) {
  items.forEach(async item => {
    await processItem(item);
  });
  console.log('Done!'); // Logs immediately, not when done!
}

// ✅ Use for...of
async function processAll(items) {
  for (const item of items) {
    await processItem(item);
  }
  console.log('Done!'); // Logs when actually done
}

// ✅ Or Promise.all for parallel
async function processAll(items) {
  await Promise.all(
    items.map(item => processItem(item))
  );
  console.log('Done!');
}
```

**Anti-Pattern 12: try/catch Without Rethrowing**

```javascript
// ❌ Catching and not rethrowing
async function getData() {
  try {
    return await fetchData();
  } catch (error) {
    console.error('Error:', error);
    // Error is swallowed!
  }
}

const data = await getData(); // undefined, no indication of error!

// ✅ Rethrow or return explicit value
async function getData() {
  try {
    return await fetchData();
  } catch (error) {
    console.error('Error:', error);
    throw error; // Let caller handle it
  }
}

// or return explicit value
async function getData() {
  try {
    return await fetchData();
  } catch (error) {
    console.error('Error:', error);
    return null; // Explicit fallback
  }
}
```

**Summary of Best Practices:**

✅ Always return promises from functions
✅ Use async/await consistently (don't mix with .then())
✅ Always handle errors with try/catch or .catch()
✅ Use Promise.all() for parallel operations
✅ Use Promise.allSettled() when some failures are okay
✅ Avoid nesting promises
✅ Don't create unnecessary promise wrappers
✅ Use for...of or Promise.all(), not forEach() with async
✅ Cache promises to avoid race conditions
✅ Rethrow errors unless explicitly handling them

---

This comprehensive guide covers all important Promise-related interview questions with practical examples and real-world scenarios.