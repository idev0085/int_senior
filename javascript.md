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
`this` refers to the object that is executing the current function. Its value depends on how the function is called.

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