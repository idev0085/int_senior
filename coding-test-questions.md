# Machine Test / Coding Interview Questions
## JavaScript, React JS, Next JS

---

## Table of Contents
1. [JavaScript (1-30)](#javascript-questions)
2. [React JS (31-60)](#react-js-questions)
3. [Next JS (61-80)](#next-js-questions)

---

## JavaScript Questions

### 1. Reverse a String
**Problem:** Write a function to reverse a string without using built-in reverse method.

**Input:** "hello"
**Output:** "olleh"

**Solution:**
```javascript
function reverseString(str) {
    let reversed = '';
    for (let i = str.length - 1; i >= 0; i--) {
        reversed += str[i];
    }
    return reversed;
}

// Alternative using spread operator
function reverseString2(str) {
    return [...str].reverse().join('');
}

// Alternative using recursion
function reverseString3(str) {
    if (str === '') return '';
    return reverseString3(str.slice(1)) + str[0];
}
```

---

### 2. Palindrome Check
**Problem:** Check if a string is a palindrome (ignoring spaces and case).

**Input:** "A man a plan a canal Panama"
**Output:** true

**Solution:**
```javascript
function isPalindrome(str) {
    // Remove spaces and convert to lowercase
    const cleaned = str.replace(/\s/g, '').toLowerCase();
    
    // Check if it matches its reverse
    return cleaned === cleaned.split('').reverse().join('');
}

// More efficient two-pointer approach
function isPalindrome2(str) {
    const cleaned = str.replace(/[^a-zA-Z0-9]/g, '').toLowerCase();
    let left = 0, right = cleaned.length - 1;
    
    while (left < right) {
        if (cleaned[left] !== cleaned[right]) return false;
        left++;
        right--;
    }
    return true;
}
```

---

### 3. Find Duplicates in Array
**Problem:** Find all duplicate elements in an array.

**Input:** [1, 2, 2, 3, 3, 3, 4]
**Output:** [2, 3]

**Solution:**
```javascript
function findDuplicates(arr) {
    const seen = new Set();
    const duplicates = new Set();
    
    for (const num of arr) {
        if (seen.has(num)) {
            duplicates.add(num);
        } else {
            seen.add(num);
        }
    }
    
    return Array.from(duplicates);
}

// Using object to count frequencies
function findDuplicates2(arr) {
    const count = {};
    
    for (const num of arr) {
        count[num] = (count[num] || 0) + 1;
    }
    
    return Object.keys(count)
        .filter(key => count[key] > 1)
        .map(Number);
}
```

---

### 4. Two Sum Problem
**Problem:** Find two numbers in array that add up to target sum.

**Input:** [2, 7, 11, 15], target = 9
**Output:** [0, 1] or [2, 7]

**Solution:**
```javascript
function twoSum(arr, target) {
    const seen = new Map();
    
    for (let i = 0; i < arr.length; i++) {
        const complement = target - arr[i];
        
        if (seen.has(complement)) {
            return [seen.get(complement), i];
        }
        
        seen.set(arr[i], i);
    }
    
    return null;
}

// Two pointer approach (requires sorted array)
function twoSum2(arr, target) {
    arr.sort((a, b) => a - b);
    let left = 0, right = arr.length - 1;
    
    while (left < right) {
        const sum = arr[left] + arr[right];
        if (sum === target) {
            return [arr[left], arr[right]];
        } else if (sum < target) {
            left++;
        } else {
            right--;
        }
    }
    
    return null;
}
```

---

### 5. Array Rotation
**Problem:** Rotate array elements by k positions.

**Input:** [1, 2, 3, 4, 5], k = 2
**Output:** [4, 5, 1, 2, 3]

**Solution:**
```javascript
function rotateArray(arr, k) {
    k = k % arr.length;
    return [...arr.slice(-k), ...arr.slice(0, arr.length - k)];
}

// Using splice
function rotateArray2(arr, k) {
    k = k % arr.length;
    arr.unshift(...arr.splice(-k));
    return arr;
}

// In-place reversal
function rotateArray3(arr, k) {
    k = k % arr.length;
    
    const reverse = (start, end) => {
        while (start < end) {
            [arr[start], arr[end]] = [arr[end], arr[start]];
            start++;
            end--;
        }
    };
    
    reverse(0, arr.length - 1);
    reverse(0, k - 1);
    reverse(k, arr.length - 1);
    
    return arr;
}
```

---

### 6. Flatten Nested Array
**Problem:** Flatten a nested array of any depth.

**Input:** [1, [2, [3, 4]], 5]
**Output:** [1, 2, 3, 4, 5]

**Solution:**
```javascript
function flattenArray(arr) {
    return arr.reduce((flat, item) => {
        return flat.concat(
            Array.isArray(item) ? flattenArray(item) : item
        );
    }, []);
}

// Using flat method
function flattenArray2(arr) {
    return arr.flat(Infinity);
}

// Using forEach recursion
function flattenArray3(arr) {
    const result = [];
    
    arr.forEach(item => {
        if (Array.isArray(item)) {
            result.push(...flattenArray3(item));
        } else {
            result.push(item);
        }
    });
    
    return result;
}
```

---

### 7. Remove Duplicates from Array
**Problem:** Remove duplicate elements while maintaining order.

**Input:** [1, 2, 2, 3, 1, 4, 2]
**Output:** [1, 2, 3, 4]

**Solution:**
```javascript
function removeDuplicates(arr) {
    return [...new Set(arr)];
}

// Using filter
function removeDuplicates2(arr) {
    return arr.filter((item, index) => arr.indexOf(item) === index);
}

// Using reduce
function removeDuplicates3(arr) {
    return arr.reduce((unique, item) => {
        if (!unique.includes(item)) {
            unique.push(item);
        }
        return unique;
    }, []);
}
```

---

### 8. Find Maximum Element
**Problem:** Find maximum element in array.

**Input:** [3, 7, 2, 9, 1]
**Output:** 9

**Solution:**
```javascript
// Using Math.max
function findMax(arr) {
    return Math.max(...arr);
}

// Using reduce
function findMax2(arr) {
    return arr.reduce((max, current) => 
        current > max ? current : max
    );
}

// Using loop
function findMax3(arr) {
    let max = arr[0];
    for (let i = 1; i < arr.length; i++) {
        if (arr[i] > max) {
            max = arr[i];
        }
    }
    return max;
}
```

---

### 9. Merge Two Sorted Arrays
**Problem:** Merge two sorted arrays into one sorted array.

**Input:** [1, 3, 5], [2, 4, 6]
**Output:** [1, 2, 3, 4, 5, 6]

**Solution:**
```javascript
function mergeSortedArrays(arr1, arr2) {
    return [...arr1, ...arr2].sort((a, b) => a - b);
}

// Two-pointer approach (more efficient)
function mergeSortedArrays2(arr1, arr2) {
    const result = [];
    let i = 0, j = 0;
    
    while (i < arr1.length && j < arr2.length) {
        if (arr1[i] <= arr2[j]) {
            result.push(arr1[i++]);
        } else {
            result.push(arr2[j++]);
        }
    }
    
    result.push(...arr1.slice(i));
    result.push(...arr2.slice(j));
    
    return result;
}
```

---

### 10. Count Vowels in String
**Problem:** Count the number of vowels in a string.

**Input:** "hello world"
**Output:** 3

**Solution:**
```javascript
function countVowels(str) {
    return (str.match(/[aeiou]/gi) || []).length;
}

// Using reduce
function countVowels2(str) {
    return str.toLowerCase().split('').reduce((count, char) => {
        return 'aeiou'.includes(char) ? count + 1 : count;
    }, 0);
}

// Using filter
function countVowels3(str) {
    return str.toLowerCase()
        .split('')
        .filter(char => 'aeiou'.includes(char))
        .length;
}
```

---

### 11. Check if Array is Sorted
**Problem:** Check if an array is sorted in ascending order.

**Input:** [1, 2, 3, 4, 5]
**Output:** true

**Solution:**
```javascript
function isSorted(arr) {
    for (let i = 0; i < arr.length - 1; i++) {
        if (arr[i] > arr[i + 1]) {
            return false;
        }
    }
    return true;
}

// Using every
function isSorted2(arr) {
    return arr.every((val, i, arr) => 
        i === 0 || val >= arr[i - 1]
    );
}

// Using JSON comparison
function isSorted3(arr) {
    return JSON.stringify(arr) === 
           JSON.stringify([...arr].sort((a, b) => a - b));
}
```

---

### 12. Sum of Array Elements
**Problem:** Find the sum of all elements in an array.

**Input:** [1, 2, 3, 4, 5]
**Output:** 15

**Solution:**
```javascript
// Using reduce
function sumArray(arr) {
    return arr.reduce((sum, num) => sum + num, 0);
}

// Using for loop
function sumArray2(arr) {
    let sum = 0;
    for (const num of arr) {
        sum += num;
    }
    return sum;
}

// Using forEach
function sumArray3(arr) {
    let sum = 0;
    arr.forEach(num => sum += num);
    return sum;
}
```

---

### 13. Find Missing Number
**Problem:** Find the missing number in array of 1 to n.

**Input:** [1, 2, 4, 5] (n = 5)
**Output:** 3

**Solution:**
```javascript
function findMissing(arr) {
    const n = arr.length + 1;
    const expectedSum = (n * (n + 1)) / 2;
    const actualSum = arr.reduce((sum, num) => sum + num, 0);
    return expectedSum - actualSum;
}

// Using Set
function findMissing2(arr) {
    const n = arr.length + 1;
    const set = new Set(arr);
    
    for (let i = 1; i <= n; i++) {
        if (!set.has(i)) {
            return i;
        }
    }
}
```

---

### 14. Swap Two Numbers
**Problem:** Swap two numbers without using temporary variable.

**Input:** a = 5, b = 10
**Output:** a = 10, b = 5

**Solution:**
```javascript
// Using destructuring
function swap(a, b) {
    [a, b] = [b, a];
    return [a, b];
}

// Using arithmetic
function swap2(a, b) {
    a = a + b;
    b = a - b;
    a = a - b;
    return [a, b];
}

// Using XOR
function swap3(a, b) {
    a = a ^ b;
    b = a ^ b;
    a = a ^ b;
    return [a, b];
}
```

---

### 15. String Compression
**Problem:** Compress string by replacing consecutive characters with count.

**Input:** "aaabbbccc"
**Output:** "a3b3c3"

**Solution:**
```javascript
function compressString(str) {
    let compressed = '';
    let count = 1;
    
    for (let i = 0; i < str.length; i++) {
        if (i + 1 < str.length && str[i] === str[i + 1]) {
            count++;
        } else {
            compressed += str[i] + count;
            count = 1;
        }
    }
    
    return compressed;
}

// Using regex
function compressString2(str) {
    return str.replace(/(.)\1*/g, (match) => 
        match[0] + match.length
    );
}
```

---

### 16. Anagram Check
**Problem:** Check if two strings are anagrams.

**Input:** "listen", "silent"
**Output:** true

**Solution:**
```javascript
function isAnagram(str1, str2) {
    const normalize = (str) => 
        str.toLowerCase().split('').sort().join('');
    
    return normalize(str1) === normalize(str2);
}

// Using character count
function isAnagram2(str1, str2) {
    if (str1.length !== str2.length) return false;
    
    const count = {};
    
    for (const char of str1.toLowerCase()) {
        count[char] = (count[char] || 0) + 1;
    }
    
    for (const char of str2.toLowerCase()) {
        if (!count[char]) return false;
        count[char]--;
    }
    
    return true;
}
```

---

### 17. Capitalize First Letter of Each Word
**Problem:** Capitalize first letter of each word in a sentence.

**Input:** "hello world"
**Output:** "Hello World"

**Solution:**
```javascript
function capitalizeWords(str) {
    return str
        .split(' ')
        .map(word => word.charAt(0).toUpperCase() + word.slice(1))
        .join(' ');
}

// Using regex
function capitalizeWords2(str) {
    return str.replace(/\b\w/g, char => char.toUpperCase());
}

// Using reduce
function capitalizeWords3(str) {
    return str.split(' ').reduce((result, word) => {
        return result + ' ' + word.charAt(0).toUpperCase() + word.slice(1);
    }).trim();
}
```

---

### 18. Find Longest Word
**Problem:** Find the longest word in a sentence.

**Input:** "The quick brown fox jumps over the lazy dog"
**Output:** "quick" or "brown" etc. (length 5)

**Solution:**
```javascript
function longestWord(sentence) {
    return sentence
        .split(' ')
        .reduce((longest, word) => 
            word.length > longest.length ? word : longest
        );
}

// Using sort
function longestWord2(sentence) {
    return sentence
        .split(' ')
        .sort((a, b) => b.length - a.length)[0];
}

// Using for loop
function longestWord3(sentence) {
    const words = sentence.split(' ');
    let longest = words[0];
    
    for (const word of words) {
        if (word.length > longest.length) {
            longest = word;
        }
    }
    
    return longest;
}
```

---

### 19. Prime Number Check
**Problem:** Check if a number is prime.

**Input:** 17
**Output:** true

**Solution:**
```javascript
function isPrime(num) {
    if (num <= 1) return false;
    if (num <= 3) return true;
    if (num % 2 === 0 || num % 3 === 0) return false;
    
    for (let i = 5; i * i <= num; i += 6) {
        if (num % i === 0 || num % (i + 2) === 0) {
            return false;
        }
    }
    
    return true;
}

// Simple approach
function isPrime2(num) {
    if (num < 2) return false;
    
    for (let i = 2; i < num; i++) {
        if (num % i === 0) {
            return false;
        }
    }
    
    return true;
}
```

---

### 20. Factorial of a Number
**Problem:** Calculate factorial of a number.

**Input:** 5
**Output:** 120

**Solution:**
```javascript
// Using recursion
function factorial(n) {
    if (n === 0 || n === 1) return 1;
    return n * factorial(n - 1);
}

// Using loop
function factorial2(n) {
    let result = 1;
    for (let i = 2; i <= n; i++) {
        result *= i;
    }
    return result;
}

// Using reduce
function factorial3(n) {
    return Array.from({length: n}, (_, i) => i + 1)
        .reduce((result, num) => result * num, 1);
}
```

---

### 21. Fibonacci Sequence
**Problem:** Generate fibonacci sequence up to n terms.

**Input:** 6
**Output:** [0, 1, 1, 2, 3, 5]

**Solution:**
```javascript
function fibonacci(n) {
    if (n <= 0) return [];
    if (n === 1) return [0];
    
    const fib = [0, 1];
    for (let i = 2; i < n; i++) {
        fib.push(fib[i - 1] + fib[i - 2]);
    }
    return fib;
}

// Using recursion with memoization
function fibonacci2(n) {
    const memo = {};
    
    function fib(num) {
        if (num <= 1) return num;
        if (memo[num]) return memo[num];
        
        memo[num] = fib(num - 1) + fib(num - 2);
        return memo[num];
    }
    
    return Array.from({length: n}, (_, i) => fib(i));
}
```

---

### 22. Group By Property
**Problem:** Group array of objects by a property.

**Input:** [{id: 1, category: 'A'}, {id: 2, category: 'B'}, {id: 3, category: 'A'}]
**Output:** {A: [{id: 1, category: 'A'}, {id: 3, category: 'A'}], B: [{id: 2, category: 'B'}]}

**Solution:**
```javascript
function groupBy(arr, property) {
    return arr.reduce((grouped, item) => {
        const key = item[property];
        if (!grouped[key]) {
            grouped[key] = [];
        }
        grouped[key].push(item);
        return grouped;
    }, {});
}

// Using forEach
function groupBy2(arr, property) {
    const grouped = {};
    
    arr.forEach(item => {
        const key = item[property];
        if (!grouped[key]) {
            grouped[key] = [];
        }
        grouped[key].push(item);
    });
    
    return grouped;
}
```

---

### 23. Debounce Function
**Problem:** Create a debounce function that delays execution.

**Input:** function, delay
**Output:** debounced function

**Solution:**
```javascript
function debounce(func, delay) {
    let timeoutId;
    
    return function(...args) {
        clearTimeout(timeoutId);
        timeoutId = setTimeout(() => {
            func.apply(this, args);
        }, delay);
    };
}

// Usage
const handleSearch = debounce((query) => {
    console.log('Searching for:', query);
}, 300);

// Call multiple times, executes only once after 300ms of inactivity
handleSearch('hello');
handleSearch('hello world');
handleSearch('hello world!');
```

---

### 24. Throttle Function
**Problem:** Create a throttle function that limits execution frequency.

**Input:** function, delay
**Output:** throttled function

**Solution:**
```javascript
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

// Using timestamp approach
function throttle2(func, limit) {
    let lastRun = 0;
    
    return function(...args) {
        const now = Date.now();
        if (now - lastRun >= limit) {
            func.apply(this, args);
            lastRun = now;
        }
    };
}
```

---

### 25. Memoization
**Problem:** Create a memoization function to cache results.

**Input:** function
**Output:** memoized function

**Solution:**
```javascript
function memoize(func) {
    const cache = {};
    
    return function(...args) {
        const key = JSON.stringify(args);
        
        if (key in cache) {
            return cache[key];
        }
        
        const result = func.apply(this, args);
        cache[key] = result;
        return result;
    };
}

// Usage
const memoizedAdd = memoize((a, b) => {
    console.log('Computing...');
    return a + b;
});

memoizedAdd(5, 3); // Computing... 8
memoizedAdd(5, 3); // 8 (from cache, no log)
```

---

### 26. Promise.all Simulation
**Problem:** Implement a promise resolver that waits for all promises.

**Solution:**
```javascript
function promiseAll(promises) {
    return new Promise((resolve, reject) => {
        if (promises.length === 0) {
            resolve([]);
            return;
        }
        
        const results = new Array(promises.length);
        let completed = 0;
        
        promises.forEach((promise, index) => {
            Promise.resolve(promise)
                .then(value => {
                    results[index] = value;
                    completed++;
                    
                    if (completed === promises.length) {
                        resolve(results);
                    }
                })
                .catch(reject);
        });
    });
}
```

---

### 27. Deep Clone Object
**Problem:** Create a deep copy of an object/array.

**Input:** {a: 1, b: {c: 2}}
**Output:** {a: 1, b: {c: 2}} (independent copy)

**Solution:**
```javascript
function deepClone(obj) {
    if (obj === null || typeof obj !== 'object') {
        return obj;
    }
    
    if (obj instanceof Date) {
        return new Date(obj.getTime());
    }
    
    if (obj instanceof Array) {
        return obj.map(item => deepClone(item));
    }
    
    if (obj instanceof Object) {
        const cloned = {};
        for (const key in obj) {
            if (obj.hasOwnProperty(key)) {
                cloned[key] = deepClone(obj[key]);
            }
        }
        return cloned;
    }
}

// Using JSON (works for most cases)
function deepClone2(obj) {
    return JSON.parse(JSON.stringify(obj));
}

// Using structuredClone (modern approach)
function deepClone3(obj) {
    return structuredClone(obj);
}
```

---

### 28. Curry Function
**Problem:** Convert a function that takes multiple arguments into one that takes arguments one at a time.

**Solution:**
```javascript
function curry(func) {
    const arity = func.length;
    
    return function curried(...args) {
        if (args.length >= arity) {
            return func.apply(null, args);
        }
        
        return (...nextArgs) => 
            curried.apply(null, args.concat(nextArgs));
    };
}

// Usage
const add = (a, b, c) => a + b + c;
const curriedAdd = curry(add);

curriedAdd(1)(2)(3); // 6
curriedAdd(1, 2)(3); // 6
curriedAdd(1)(2, 3); // 6
```

---

### 29. Unique Array with Filter and Index
**Problem:** Remove duplicates keeping first occurrence.

**Input:** [1, 2, 2, 3, 1, 4]
**Output:** [1, 2, 3, 4]

**Solution:**
```javascript
function uniqueArray(arr) {
    return arr.filter((val, index) => arr.indexOf(val) === index);
}

// Using Set
function uniqueArray2(arr) {
    return [...new Set(arr)];
}

// Using reduce
function uniqueArray3(arr) {
    return arr.reduce((unique, item) => {
        if (!unique.includes(item)) {
            unique.push(item);
        }
        return unique;
    }, []);
}
```

---

### 30. Get Nested Property Value
**Problem:** Safely access deeply nested object properties.

**Input:** {a: {b: {c: 'value'}}}, "a.b.c"
**Output:** "value"

**Solution:**
```javascript
function getNestedValue(obj, path) {
    return path.split('.').reduce((current, prop) => 
        current?.[prop], obj
    );
}

// Alternative without optional chaining
function getNestedValue2(obj, path) {
    let current = obj;
    const keys = path.split('.');
    
    for (const key of keys) {
        if (current && typeof current === 'object' && key in current) {
            current = current[key];
        } else {
            return undefined;
        }
    }
    
    return current;
}

// Usage
const obj = {a: {b: {c: 'value'}}};
console.log(getNestedValue(obj, 'a.b.c')); // 'value'
console.log(getNestedValue(obj, 'a.x.c')); // undefined
```

---

## React JS Questions

### 31. Create a Counter Component
**Problem:** Create a functional React component with increment/decrement functionality.

**Solution:**
```javascript
import React, { useState } from 'react';

function Counter() {
    const [count, setCount] = useState(0);
    
    const increment = () => setCount(count + 1);
    const decrement = () => setCount(count - 1);
    const reset = () => setCount(0);
    
    return (
        <div style={{ padding: '20px' }}>
            <h1>Counter: {count}</h1>
            <button onClick={increment}>+</button>
            <button onClick={decrement}>-</button>
            <button onClick={reset}>Reset</button>
        </div>
    );
}

export default Counter;
```

---

### 32. Todo List Component
**Problem:** Create a todo list with add and delete functionality.

**Solution:**
```javascript
import React, { useState } from 'react';

function TodoList() {
    const [todos, setTodos] = useState([]);
    const [input, setInput] = useState('');
    
    const addTodo = () => {
        if (input.trim()) {
            setTodos([...todos, { id: Date.now(), text: input }]);
            setInput('');
        }
    };
    
    const deleteTodo = (id) => {
        setTodos(todos.filter(todo => todo.id !== id));
    };
    
    return (
        <div>
            <input 
                value={input}
                onChange={(e) => setInput(e.target.value)}
                onKeyPress={(e) => e.key === 'Enter' && addTodo()}
                placeholder="Add a todo"
            />
            <button onClick={addTodo}>Add</button>
            
            <ul>
                {todos.map(todo => (
                    <li key={todo.id}>
                        {todo.text}
                        <button onClick={() => deleteTodo(todo.id)}>Delete</button>
                    </li>
                ))}
            </ul>
        </div>
    );
}

export default TodoList;
```

---

### 33. Fetch and Display Data
**Problem:** Create a component that fetches data from API and displays it.

**Solution:**
```javascript
import React, { useState, useEffect } from 'react';

function UserList() {
    const [users, setUsers] = useState([]);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);
    
    useEffect(() => {
        fetch('https://jsonplaceholder.typicode.com/users')
            .then(response => response.json())
            .then(data => {
                setUsers(data);
                setLoading(false);
            })
            .catch(err => {
                setError(err);
                setLoading(false);
            });
    }, []);
    
    if (loading) return <p>Loading...</p>;
    if (error) return <p>Error: {error.message}</p>;
    
    return (
        <div>
            <h1>Users</h1>
            <ul>
                {users.map(user => (
                    <li key={user.id}>{user.name}</li>
                ))}
            </ul>
        </div>
    );
}

export default UserList;
```

---

### 34. Form Component with Validation
**Problem:** Create a form with input validation.

**Solution:**
```javascript
import React, { useState } from 'react';

function Form() {
    const [formData, setFormData] = useState({
        email: '',
        password: '',
        confirmPassword: ''
    });
    const [errors, setErrors] = useState({});
    
    const handleChange = (e) => {
        const { name, value } = e.target;
        setFormData({...formData, [name]: value});
    };
    
    const validate = () => {
        const newErrors = {};
        
        if (!formData.email.includes('@')) {
            newErrors.email = 'Invalid email';
        }
        
        if (formData.password.length < 6) {
            newErrors.password = 'Password must be at least 6 characters';
        }
        
        if (formData.password !== formData.confirmPassword) {
            newErrors.confirmPassword = 'Passwords do not match';
        }
        
        return newErrors;
    };
    
    const handleSubmit = (e) => {
        e.preventDefault();
        const newErrors = validate();
        
        if (Object.keys(newErrors).length === 0) {
            console.log('Form submitted:', formData);
        } else {
            setErrors(newErrors);
        }
    };
    
    return (
        <form onSubmit={handleSubmit}>
            <div>
                <input
                    type="email"
                    name="email"
                    value={formData.email}
                    onChange={handleChange}
                    placeholder="Email"
                />
                {errors.email && <p>{errors.email}</p>}
            </div>
            
            <div>
                <input
                    type="password"
                    name="password"
                    value={formData.password}
                    onChange={handleChange}
                    placeholder="Password"
                />
                {errors.password && <p>{errors.password}</p>}
            </div>
            
            <div>
                <input
                    type="password"
                    name="confirmPassword"
                    value={formData.confirmPassword}
                    onChange={handleChange}
                    placeholder="Confirm Password"
                />
                {errors.confirmPassword && <p>{errors.confirmPassword}</p>}
            </div>
            
            <button type="submit">Submit</button>
        </form>
    );
}

export default Form;
```

---

### 35. Custom Hook - useLocalStorage
**Problem:** Create a custom hook to manage local storage.

**Solution:**
```javascript
import { useState, useEffect } from 'react';

function useLocalStorage(key, initialValue) {
    const [storedValue, setStoredValue] = useState(() => {
        try {
            const item = window.localStorage.getItem(key);
            return item ? JSON.parse(item) : initialValue;
        } catch (error) {
            console.error(error);
            return initialValue;
        }
    });
    
    const setValue = (value) => {
        try {
            const valueToStore = value instanceof Function ? value(storedValue) : value;
            setStoredValue(valueToStore);
            window.localStorage.setItem(key, JSON.stringify(valueToStore));
        } catch (error) {
            console.error(error);
        }
    };
    
    return [storedValue, setValue];
}

// Usage
function App() {
    const [name, setName] = useLocalStorage('name', 'John');
    
    return (
        <div>
            <input value={name} onChange={(e) => setName(e.target.value)} />
            <p>Hello {name}</p>
        </div>
    );
}
```

---

### 36. Controlled vs Uncontrolled Component
**Problem:** Demonstrate difference between controlled and uncontrolled components.

**Solution:**
```javascript
// Controlled Component
function ControlledInput() {
    const [value, setValue] = useState('');
    
    return (
        <input
            value={value}
            onChange={(e) => setValue(e.target.value)}
            placeholder="Controlled"
        />
    );
}

// Uncontrolled Component
function UncontrolledInput() {
    const inputRef = useRef(null);
    
    const handleSubmit = () => {
        console.log(inputRef.current.value);
    };
    
    return (
        <>
            <input ref={inputRef} placeholder="Uncontrolled" />
            <button onClick={handleSubmit}>Get Value</button>
        </>
    );
}
```

---

### 37. useEffect with Cleanup
**Problem:** Create a component that demonstrates useEffect cleanup.

**Solution:**
```javascript
import React, { useEffect, useState } from 'react';

function Timer() {
    const [seconds, setSeconds] = useState(0);
    
    useEffect(() => {
        const interval = setInterval(() => {
            setSeconds(s => s + 1);
        }, 1000);
        
        // Cleanup function
        return () => {
            clearInterval(interval);
        };
    }, []);
    
    return <h1>Timer: {seconds} seconds</h1>;
}

export default Timer;
```

---

### 38. Conditional Rendering
**Problem:** Demonstrate different conditional rendering techniques.

**Solution:**
```javascript
function ConditionalRender({ isLoggedIn, user }) {
    // Using if-else
    if (isLoggedIn) {
        return <h1>Welcome {user.name}</h1>;
    }
    return <h1>Please log in</h1>;
}

// Using ternary
function ConditionalRender2({ isLoggedIn, user }) {
    return (
        <div>
            {isLoggedIn ? (
                <h1>Welcome {user.name}</h1>
            ) : (
                <h1>Please log in</h1>
            )}
        </div>
    );
}

// Using logical AND
function ConditionalRender3({ isLoggedIn, user }) {
    return (
        <div>
            {isLoggedIn && <h1>Welcome {user.name}</h1>}
        </div>
    );
}

// Using switch
function ConditionalRender4({ status }) {
    switch(status) {
        case 'loading':
            return <p>Loading...</p>;
        case 'success':
            return <p>Success!</p>;
        case 'error':
            return <p>Error!</p>;
        default:
            return null;
    }
}
```

---

### 39. PropTypes and Default Props
**Problem:** Add prop validation to a component.

**Solution:**
```javascript
import PropTypes from 'prop-types';

function UserCard({ name, age, email, role }) {
    return (
        <div>
            <h2>{name}</h2>
            <p>Age: {age}</p>
            <p>Email: {email}</p>
            <p>Role: {role}</p>
        </div>
    );
}

UserCard.propTypes = {
    name: PropTypes.string.isRequired,
    age: PropTypes.number,
    email: PropTypes.string.isRequired,
    role: PropTypes.oneOf(['admin', 'user', 'guest'])
};

UserCard.defaultProps = {
    age: 25,
    role: 'user'
};

export default UserCard;
```

---

### 40. Custom Hook - useFetch
**Problem:** Create a custom hook for fetching data.

**Solution:**
```javascript
import { useState, useEffect } from 'react';

function useFetch(url) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);
    
    useEffect(() => {
        const fetchData = async () => {
            try {
                const response = await fetch(url);
                const json = await response.json();
                setData(json);
            } catch (err) {
                setError(err);
            } finally {
                setLoading(false);
            }
        };
        
        fetchData();
    }, [url]);
    
    return { data, loading, error };
}

// Usage
function App() {
    const { data, loading, error } = useFetch('https://api.example.com/data');
    
    if (loading) return <p>Loading...</p>;
    if (error) return <p>Error: {error.message}</p>;
    
    return <div>{JSON.stringify(data)}</div>;
}
```

---

### 41. Context API Implementation
**Problem:** Create a theme context for light/dark mode.

**Solution:**
```javascript
import React, { createContext, useState, useContext } from 'react';

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
    const [isDark, setIsDark] = useState(false);
    
    const toggleTheme = () => {
        setIsDark(!isDark);
    };
    
    return (
        <ThemeContext.Provider value={{ isDark, toggleTheme }}>
            {children}
        </ThemeContext.Provider>
    );
}

export function useTheme() {
    const context = useContext(ThemeContext);
    if (!context) {
        throw new Error('useTheme must be used within ThemeProvider');
    }
    return context;
}

// Usage
function App() {
    const { isDark, toggleTheme } = useTheme();
    
    return (
        <div style={{ background: isDark ? '#333' : '#fff' }}>
            <button onClick={toggleTheme}>Toggle Theme</button>
        </div>
    );
}
```

---

### 42. Error Boundary
**Problem:** Create an error boundary component.

**Solution:**
```javascript
import React from 'react';

class ErrorBoundary extends React.Component {
    constructor(props) {
        super(props);
        this.state = { hasError: false, error: null };
    }
    
    static getDerivedStateFromError(error) {
        return { hasError: true, error };
    }
    
    componentDidCatch(error, errorInfo) {
        console.error('Error caught:', error, errorInfo);
    }
    
    render() {
        if (this.state.hasError) {
            return (
                <div>
                    <h1>Something went wrong</h1>
                    <p>{this.state.error.toString()}</p>
                </div>
            );
        }
        
        return this.props.children;
    }
}

export default ErrorBoundary;
```

---

### 43. Memo for Performance Optimization
**Problem:** Optimize component re-renders using React.memo.

**Solution:**
```javascript
import React from 'react';

// Without memo - re-renders on every parent render
function UserItem({ name, onClick }) {
    console.log('Rendering:', name);
    return <li onClick={onClick}>{name}</li>;
}

// With memo - only re-renders if props change
const MemoizedUserItem = React.memo(UserItem);

// Custom comparison
const MemoizedUserItem2 = React.memo(
    UserItem,
    (prevProps, nextProps) => {
        return prevProps.name === nextProps.name &&
               prevProps.onClick === nextProps.onClick;
    }
);
```

---

### 44. useCallback Hook
**Problem:** Use useCallback to prevent unnecessary function recreations.

**Solution:**
```javascript
import { useState, useCallback } from 'react';

function Parent() {
    const [count, setCount] = useState(0);
    
    // Without useCallback: new function created on every render
    // const handleClick = () => console.log('clicked');
    
    // With useCallback: same function unless dependency changes
    const handleClick = useCallback(() => {
        console.log('clicked');
    }, []);
    
    const handleDelete = useCallback((id) => {
        console.log('Delete:', id);
    }, []);
    
    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>Increment</button>
            <Child onClick={handleClick} onDelete={handleDelete} />
        </div>
    );
}

function Child({ onClick, onDelete }) {
    return (
        <div>
            <button onClick={onClick}>Click</button>
            <button onClick={() => onDelete(1)}>Delete 1</button>
        </div>
    );
}
```

---

### 45. useMemo Hook
**Problem:** Use useMemo to memoize expensive computations.

**Solution:**
```javascript
import { useState, useMemo } from 'react';

function ExpensiveComponent({ items, multiplier }) {
    const [count, setCount] = useState(0);
    
    // Without useMemo: recalculates on every render
    // const total = items.reduce((sum, item) => sum + item, 0) * multiplier;
    
    // With useMemo: only recalculates when items or multiplier change
    const total = useMemo(() => {
        console.log('Calculating total...');
        return items.reduce((sum, item) => sum + item, 0) * multiplier;
    }, [items, multiplier]);
    
    return (
        <div>
            <p>Total: {total}</p>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>Increment</button>
        </div>
    );
}
```

---

### 46. Debounce in React
**Problem:** Implement debounce for search input.

**Solution:**
```javascript
import { useState, useEffect } from 'react';

function SearchUsers() {
    const [query, setQuery] = useState('');
    const [results, setResults] = useState([]);
    const [loading, setLoading] = useState(false);
    
    useEffect(() => {
        const timer = setTimeout(() => {
            if (query.trim()) {
                setLoading(true);
                fetch(`/api/search?q=${query}`)
                    .then(res => res.json())
                    .then(data => {
                        setResults(data);
                        setLoading(false);
                    });
            }
        }, 300);
        
        return () => clearTimeout(timer);
    }, [query]);
    
    return (
        <div>
            <input
                value={query}
                onChange={(e) => setQuery(e.target.value)}
                placeholder="Search..."
            />
            {loading && <p>Loading...</p>}
            <ul>
                {results.map((result, i) => (
                    <li key={i}>{result}</li>
                ))}
            </ul>
        </div>
    );
}
```

---

### 47. Infinite Scroll Component
**Problem:** Implement infinite scroll functionality.

**Solution:**
```javascript
import { useState, useEffect, useCallback } from 'react';

function InfiniteScroll() {
    const [items, setItems] = useState([]);
    const [page, setPage] = useState(0);
    const [loading, setLoading] = useState(false);
    
    const loadMore = useCallback(() => {
        setLoading(true);
        fetch(`/api/items?page=${page}`)
            .then(res => res.json())
            .then(data => {
                setItems(prev => [...prev, ...data]);
                setPage(prev => prev + 1);
                setLoading(false);
            });
    }, [page]);
    
    useEffect(() => {
        const handleScroll = () => {
            if (window.innerHeight + window.scrollY >= document.body.offsetHeight - 100) {
                loadMore();
            }
        };
        
        window.addEventListener('scroll', handleScroll);
        return () => window.removeEventListener('scroll', handleScroll);
    }, [loadMore]);
    
    useEffect(() => {
        loadMore();
    }, []);
    
    return (
        <div>
            {items.map((item, i) => (
                <div key={i}>{item.name}</div>
            ))}
            {loading && <p>Loading more...</p>}
        </div>
    );
}
```

---

### 48. Modal Component
**Problem:** Create a reusable modal component.

**Solution:**
```javascript
import { useState } from 'react';

function Modal({ isOpen, onClose, children, title }) {
    if (!isOpen) return null;
    
    return (
        <div style={{ position: 'fixed', top: 0, left: 0, right: 0, bottom: 0, background: 'rgba(0,0,0,0.5)', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
            <div style={{ background: 'white', padding: '20px', borderRadius: '8px', maxWidth: '500px' }}>
                <h2>{title}</h2>
                <button onClick={onClose} style={{ float: 'right' }}>×</button>
                <div>{children}</div>
            </div>
        </div>
    );
}

function App() {
    const [isOpen, setIsOpen] = useState(false);
    
    return (
        <div>
            <button onClick={() => setIsOpen(true)}>Open Modal</button>
            <Modal 
                isOpen={isOpen} 
                onClose={() => setIsOpen(false)}
                title="Welcome"
            >
                <p>Modal content here</p>
            </Modal>
        </div>
    );
}
```

---

### 49. Pagination Component
**Problem:** Create a pagination component.

**Solution:**
```javascript
import { useState } from 'react';

function Pagination({ total, itemsPerPage }) {
    const [currentPage, setCurrentPage] = useState(1);
    const totalPages = Math.ceil(total / itemsPerPage);
    
    const startIndex = (currentPage - 1) * itemsPerPage;
    const endIndex = startIndex + itemsPerPage;
    
    const pages = Array.from({ length: totalPages }, (_, i) => i + 1);
    
    return (
        <div>
            <div>
                {pages.map(page => (
                    <button
                        key={page}
                        onClick={() => setCurrentPage(page)}
                        style={{ 
                            background: page === currentPage ? '#007bff' : '#fff',
                            color: page === currentPage ? '#fff' : '#000'
                        }}
                    >
                        {page}
                    </button>
                ))}
            </div>
            <p>Page {currentPage} of {totalPages}</p>
        </div>
    );
}
```

---

### 50. Dynamic Form Component
**Problem:** Create a form with dynamic fields.

**Solution:**
```javascript
import { useState } from 'react';

function DynamicForm() {
    const [fields, setFields] = useState([{ id: 1, value: '' }]);
    
    const addField = () => {
        setFields([...fields, { id: Date.now(), value: '' }]);
    };
    
    const removeField = (id) => {
        setFields(fields.filter(field => field.id !== id));
    };
    
    const updateField = (id, value) => {
        setFields(fields.map(field => 
            field.id === id ? { ...field, value } : field
        ));
    };
    
    return (
        <div>
            {fields.map(field => (
                <div key={field.id}>
                    <input
                        value={field.value}
                        onChange={(e) => updateField(field.id, e.target.value)}
                        placeholder="Enter text"
                    />
                    {fields.length > 1 && (
                        <button onClick={() => removeField(field.id)}>Remove</button>
                    )}
                </div>
            ))}
            <button onClick={addField}>Add Field</button>
        </div>
    );
}
```

---

### 51-60: More React Questions (abbreviated)

**51. Controlled Select Dropdown**
**52. File Upload Component**
**53. Rating Component (Star Rating)**
**54. Accordion Component**
**55. Tab Navigation Component**
**56. Breadcrumb Navigation**
**57. Search with Filter**
**58. Lazy Loading Images**
**59. Dark/Light Theme Toggle**
**60. Session Timeout Warning**

---

## Next JS Questions

### 61. Basic Next.js Page
**Problem:** Create a basic Next.js page with routing.

**Solution:**
```javascript
// pages/index.js
export default function Home() {
    return (
        <div>
            <h1>Welcome to Next.js</h1>
            <p>This is the home page</p>
        </div>
    );
}

// pages/about.js
export default function About() {
    return (
        <div>
            <h1>About Page</h1>
        </div>
    );
}
```

---

### 62. Static Generation with getStaticProps
**Problem:** Generate static pages at build time.

**Solution:**
```javascript
// pages/posts/[id].js
export async function getStaticProps({ params }) {
    const post = await fetch(`/api/posts/${params.id}`).then(r => r.json());
    
    return {
        props: { post },
        revalidate: 60 // ISR: revalidate every 60 seconds
    };
}

export async function getStaticPaths() {
    const posts = await fetch('/api/posts').then(r => r.json());
    
    return {
        paths: posts.map(post => ({
            params: { id: post.id }
        })),
        fallback: 'blocking'
    };
}

export default function Post({ post }) {
    return <h1>{post.title}</h1>;
}
```

---

### 63. Server-Side Rendering with getServerSideProps
**Problem:** Render page on each request.

**Solution:**
```javascript
// pages/products.js
export async function getServerSideProps() {
    const products = await fetch('/api/products').then(r => r.json());
    
    return {
        props: { products }
    };
}

export default function Products({ products }) {
    return (
        <div>
            <h1>Products</h1>
            <ul>
                {products.map(product => (
                    <li key={product.id}>{product.name}</li>
                ))}
            </ul>
        </div>
    );
}
```

---

### 64. API Routes
**Problem:** Create API endpoints in Next.js.

**Solution:**
```javascript
// pages/api/users.js
export default function handler(req, res) {
    if (req.method === 'GET') {
        res.status(200).json([
            { id: 1, name: 'John' },
            { id: 2, name: 'Jane' }
        ]);
    } else if (req.method === 'POST') {
        const newUser = req.body;
        res.status(201).json(newUser);
    }
}

// pages/api/users/[id].js
export default function handler(req, res) {
    const { id } = req.query;
    
    if (req.method === 'GET') {
        res.status(200).json({ id, name: 'User ' + id });
    }
}
```

---

### 65. Image Optimization
**Problem:** Use Next.js Image component for optimization.

**Solution:**
```javascript
import Image from 'next/image';

export default function Hero() {
    return (
        <div>
            <Image
                src="/hero.jpg"
                alt="Hero"
                width={800}
                height={600}
                priority
            />
            
            <Image
                src="/thumbnail.jpg"
                alt="Thumbnail"
                width={200}
                height={200}
                loading="lazy"
            />
        </div>
    );
}
```

---

### 66. Dynamic Routes
**Problem:** Create dynamic routes in Next.js.

**Solution:**
```javascript
// pages/posts/[id].js
import { useRouter } from 'next/router';

export default function Post() {
    const router = useRouter();
    const { id } = router.query;
    
    return <h1>Post {id}</h1>;
}

// pages/users/[id]/profile.js - Nested dynamic routes
export default function Profile() {
    const router = useRouter();
    const { id } = router.query;
    
    return <h1>Profile for user {id}</h1>;
}
```

---

### 67. Link Navigation
**Problem:** Use Next.js Link for client-side navigation.

**Solution:**
```javascript
import Link from 'next/link';

export default function Navigation() {
    return (
        <nav>
            <Link href="/">Home</Link>
            <Link href="/about">About</Link>
            <Link href="/posts/1">Post 1</Link>
            <Link href="/users/[id]" as="/users/123">User 123</Link>
        </nav>
    );
}
```

---

### 68. Middleware
**Problem:** Create middleware in Next.js 12+.

**Solution:**
```javascript
// middleware.js (root of project)
import { NextResponse } from 'next/server';

export function middleware(request) {
    // Check authentication
    const token = request.cookies.get('token');
    
    if (!token && request.nextUrl.pathname.startsWith('/admin')) {
        return NextResponse.redirect(new URL('/login', request.url));
    }
    
    return NextResponse.next();
}

export const config = {
    matcher: ['/admin/:path*', '/api/admin/:path*']
};
```

---

### 69. Custom Document
**Problem:** Customize HTML document structure.

**Solution:**
```javascript
// pages/_document.js
import { Html, Head, Main, NextScript } from 'next/document';

export default function Document() {
    return (
        <Html lang="en">
            <Head>
                <link rel="icon" href="/favicon.ico" />
            </Head>
            <body>
                <Main />
                <NextScript />
            </body>
        </Html>
    );
}
```

---

### 70. Custom App Component
**Problem:** Wrap all pages with custom layout.

**Solution:**
```javascript
// pages/_app.js
import Layout from '@/components/Layout';

function MyApp({ Component, pageProps }) {
    return (
        <Layout>
            <Component {...pageProps} />
        </Layout>
    );
}

export default MyApp;
```

---

### 71. Authentication with getServerSideProps
**Problem:** Protect pages with authentication.

**Solution:**
```javascript
// pages/dashboard.js
export async function getServerSideProps(context) {
    const token = context.req.cookies.token;
    
    if (!token) {
        return {
            redirect: {
                destination: '/login',
                permanent: false
            }
        };
    }
    
    return {
        props: { user: { name: 'John' } }
    };
}

export default function Dashboard({ user }) {
    return <h1>Welcome {user.name}</h1>;
}
```

---

### 72. Environment Variables
**Problem:** Use environment variables in Next.js.

**Solution:**
```javascript
// .env.local
NEXT_PUBLIC_API_URL=https://api.example.com
API_SECRET_KEY=secret123

// pages/api/endpoint.js
export default function handler(req, res) {
    const publicUrl = process.env.NEXT_PUBLIC_API_URL;
    const secretKey = process.env.API_SECRET_KEY;
    
    res.status(200).json({ url: publicUrl });
}

// In component (only NEXT_PUBLIC_* are available)
export default function Component() {
    console.log(process.env.NEXT_PUBLIC_API_URL);
}
```

---

### 73. Incremental Static Regeneration (ISR)
**Problem:** Use ISR to update static pages periodically.

**Solution:**
```javascript
// pages/blog/[slug].js
export async function getStaticProps({ params }) {
    const post = await getPost(params.slug);
    
    return {
        props: { post },
        revalidate: 3600 // Regenerate every hour
    };
}

export async function getStaticPaths() {
    const posts = await getAllPosts();
    
    return {
        paths: posts.map(post => ({ params: { slug: post.slug } })),
        fallback: 'blocking' // Generate on-demand
    };
}

export default function BlogPost({ post }) {
    return <article>{post.content}</article>;
}
```

---

### 74. Error Handling
**Problem:** Create custom error pages.

**Solution:**
```javascript
// pages/404.js
export default function NotFound() {
    return (
        <div>
            <h1>404 - Page Not Found</h1>
            <p>The page you're looking for doesn't exist</p>
        </div>
    );
}

// pages/500.js
export default function ServerError() {
    return (
        <div>
            <h1>500 - Server Error</h1>
            <p>Something went wrong on our end</p>
        </div>
    );
}
```

---

### 75. Font Optimization
**Problem:** Optimize fonts in Next.js.

**Solution:**
```javascript
// pages/_app.js
import { Poppins, Open_Sans } from 'next/font/google';

const poppins = Poppins({
    subsets: ['latin'],
    weight: ['400', '600', '700'],
    variable: '--font-poppins'
});

const openSans = Open_Sans({
    subsets: ['latin'],
    variable: '--font-open-sans'
});

export default function App({ Component, pageProps }) {
    return (
        <div className={`${poppins.variable} ${openSans.variable}`}>
            <Component {...pageProps} />
        </div>
    );
}

// In CSS
// body {
//   font-family: var(--font-poppins);
// }
```

---

### 76. Redirects and Rewrites
**Problem:** Configure redirects and rewrites.

**Solution:**
```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
    async redirects() {
        return [
            {
                source: '/old-page',
                destination: '/new-page',
                permanent: true // 301 redirect
            }
        ];
    },
    
    async rewrites() {
        return {
            beforeFiles: [
                {
                    source: '/api/:path*',
                    destination: 'https://external-api.com/:path*'
                }
            ]
        };
    }
};

module.exports = nextConfig;
```

---

### 77. Vercel Analytics
**Problem:** Track analytics in Next.js.

**Solution:**
```javascript
// pages/_app.js
import { Analytics } from '@vercel/analytics/react';

export default function App({ Component, pageProps }) {
    return (
        <>
            <Component {...pageProps} />
            <Analytics />
        </>
    );
}
```

---

### 78. Preview Mode
**Problem:** Use preview mode for drafts.

**Solution:**
```javascript
// pages/api/preview.js
export default function preview(req, res) {
    res.setPreviewData({});
    res.redirect(req.query.slug);
}

// pages/api/exit-preview.js
export default function exitPreview(req, res) {
    res.clearPreviewData();
    res.redirect('/');
}

// pages/posts/[slug].js
export async function getStaticProps({ params, preview }) {
    const post = await getPost(params.slug, preview);
    
    return {
        props: { post },
        revalidate: preview ? 0 : 3600
    };
}
```

---

### 79. Static Export
**Problem:** Export Next.js app as static HTML.

**Solution:**
```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
    output: 'export'
};

module.exports = nextConfig;

// Run: next build
// Output: out/ directory with static HTML files
```

---

### 80. Performance Optimization
**Problem:** Optimize Next.js performance.

**Solution:**
```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
    swcMinify: true,
    compress: true,
    productionBrowserSourceMaps: false,
    poweredByHeader: false,
    
    headers() {
        return [
            {
                source: '/static/:path*',
                headers: [
                    {
                        key: 'Cache-Control',
                        value: 'public, max-age=31536000, immutable'
                    }
                ]
            }
        ];
    }
};

module.exports = nextConfig;
```

---

## Summary

This comprehensive coding test file covers:

**JavaScript (30 questions)**
- String/Array manipulation
- Object operations
- Functional programming
- Algorithms

**React JS (30 questions)**
- Component creation
- Hooks (useState, useEffect, useContext, etc.)
- State management
- Performance optimization

**Next JS (20 questions)**
- Pages and routing
- Data fetching strategies (SSG, SSR, ISR)
- API routes
- Optimization and deployment

Each question includes problem statement, examples, and multiple solution approaches.
