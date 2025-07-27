# JavaScript & TypeScript Interview Questions & Answers

## Advanced JavaScript

**1. Explain the event loop in JavaScript. How does it handle asynchronous operations?**

The JavaScript event loop is the mechanism that handles asynchronous operations in a single-threaded environment. Here's how it works:

**Components:**
- **Call Stack**: Executes synchronous code
- **Web APIs**: Handle async operations (setTimeout, DOM events, HTTP requests)
- **Callback Queue**: Stores completed async callbacks
- **Microtask Queue**: Stores promises and queueMicrotask callbacks (higher priority)

**Process:**
1. Synchronous code executes on the call stack
2. Async operations are sent to Web APIs
3. When complete, callbacks go to the appropriate queue
4. Event loop checks if call stack is empty
5. Microtasks are processed first, then macrotasks

```javascript
console.log('1'); // Synchronous

setTimeout(() => console.log('2'), 0); // Macrotask

Promise.resolve().then(() => console.log('3')); // Microtask

console.log('4'); // Synchronous

// Output: 1, 4, 3, 2
```

**2. What are closures? Provide a practical use case where they are beneficial.**

A closure is a function that has access to variables in its outer (enclosing) scope even after the outer function has returned.

**Practical Use Case - Module Pattern:**
```javascript
const createCounter = () => {
  let count = 0; // Private variable
  
  return {
    increment: () => ++count,
    decrement: () => --count,
    getCount: () => count
  };
};

const counter = createCounter();
console.log(counter.getCount()); // 0
counter.increment();
console.log(counter.getCount()); // 1
// count is not directly accessible from outside
```

**Benefits:**
- Data privacy and encapsulation
- Function factories
- Callback functions with persistent state
- Module patterns

**3. How does prototypal inheritance differ from classical inheritance?**

**Prototypal Inheritance:**
- Objects inherit directly from other objects
- No classes, only objects and prototypes
- Dynamic and flexible
- Uses prototype chain for property lookup

```javascript
const animal = {
  speak() { console.log('Animal sound'); }
};

const dog = Object.create(animal);
dog.bark = function() { console.log('Woof!'); };
```

**Classical Inheritance:**
- Classes inherit from other classes
- Static structure defined at compile time
- Uses class hierarchies
- More rigid but predictable

**Key Differences:**
- Prototypal is more flexible and dynamic
- Classical provides better tooling and IDE support
- Prototypal allows runtime modification of inheritance chain
- Classical is more familiar to developers from OOP languages

**4. Explain `this` keyword behavior in different contexts.**

```javascript
// Global context
console.log(this); // Window (browser) or global (Node.js)

// Function context
function regularFunction() {
  console.log(this); // Window (non-strict) or undefined (strict)
}

// Object method
const obj = {
  name: 'Object',
  method() {
    console.log(this.name); // 'Object'
  }
};

// Arrow function (lexical this)
const arrowObj = {
  name: 'Arrow',
  method: () => {
    console.log(this.name); // undefined (inherits from enclosing scope)
  }
};

// Class context
class MyClass {
  constructor(name) {
    this.name = name;
  }
  
  method() {
    console.log(this.name); // Instance name
  }
}

// Explicit binding
const boundFunction = regularFunction.bind(obj);
regularFunction.call(obj);
regularFunction.apply(obj, []);
```

**5. How would you optimize performance for a heavy computational task in JavaScript?**

**Strategies:**

1. **Web Workers for CPU-intensive tasks:**
```javascript
// main.js
const worker = new Worker('worker.js');
worker.postMessage({ data: largeDataSet });
worker.onmessage = (e) => {
  console.log('Result:', e.data);
};

// worker.js
self.onmessage = (e) => {
  const result = heavyComputation(e.data);
  self.postMessage(result);
};
```

2. **Time slicing with requestIdleCallback:**
```javascript
function processLargeArray(array, callback) {
  const chunk = array.splice(0, 100);
  
  // Process chunk
  chunk.forEach(processItem);
  
  if (array.length > 0) {
    requestIdleCallback(() => processLargeArray(array, callback));
  } else {
    callback();
  }
}
```

3. **Memoization:**
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
```

---

## TypeScript

**1. What are the key advantages of TypeScript over JavaScript for large-scale applications?**

**Key Advantages:**

1. **Static Type Checking:**
   - Catches errors at compile time
   - Reduces runtime errors
   - Better IDE support with autocomplete

2. **Enhanced Developer Experience:**
   - IntelliSense and code completion
   - Refactoring tools
   - Better navigation

3. **Code Documentation:**
   - Types serve as documentation
   - Self-documenting APIs
   - Easier onboarding for new developers

4. **Scalability:**
   - Better code organization
   - Interface contracts
   - Easier maintenance of large codebases

5. **Modern JavaScript Features:**
   - Latest ECMAScript features
   - Transpilation to older JavaScript versions
   - Decorators and advanced features

**2. Explain generics in TypeScript. Provide an example where they are useful.**

Generics allow you to create reusable components that work with multiple types while maintaining type safety.

```typescript
// Generic function
function identity<T>(arg: T): T {
  return arg;
}

// Generic interface
interface Repository<T> {
  findById(id: string): Promise<T>;
  save(entity: T): Promise<T>;
  delete(id: string): Promise<void>;
}

// Generic class implementation
class UserRepository implements Repository<User> {
  async findById(id: string): Promise<User> {
    // Implementation
  }
  
  async save(user: User): Promise<User> {
    // Implementation
  }
  
  async delete(id: string): Promise<void> {
    // Implementation
  }
}

// Generic constraints
interface Lengthwise {
  length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}
```

**3. How do you handle third-party libraries that lack TypeScript definitions?**

**Solutions:**

1. **Install @types packages:**
```bash
npm install @types/library-name
```

2. **Create custom declaration files:**
```typescript
// types/library-name.d.ts
declare module 'library-name' {
  export interface LibraryOptions {
    option1: string;
    option2?: number;
  }
  
  export default function libraryFunction(options: LibraryOptions): void;
}
```

3. **Use module augmentation:**
```typescript
declare module 'existing-library' {
  interface ExistingInterface {
    newProperty: string;
  }
}
```

4. **Quick any type (temporary solution):**
```typescript
declare const libraryName: any;
```

**4. What are utility types? Provide use cases.**

Utility types are built-in TypeScript types that help transform existing types.

```typescript
interface User {
  id: string;
  name: string;
  email: string;
  password: string;
}

// Partial - makes all properties optional
type UserUpdate = Partial<User>;
// { id?: string; name?: string; email?: string; password?: string; }

// Pick - selects specific properties
type UserPublic = Pick<User, 'id' | 'name' | 'email'>;
// { id: string; name: string; email: string; }

// Omit - excludes specific properties
type UserWithoutPassword = Omit<User, 'password'>;
// { id: string; name: string; email: string; }

// Record - creates object type with specific keys and values
type UserRoles = Record<string, 'admin' | 'user' | 'guest'>;
// { [key: string]: 'admin' | 'user' | 'guest'; }

// Required - makes all properties required
type RequiredUser = Required<Partial<User>>;

// Readonly - makes all properties readonly
type ImmutableUser = Readonly<User>;
```

**5. How would you enforce strict type safety in a large codebase?**

**Strategies:**

1. **Strict TypeScript configuration:**
```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noImplicitReturns": true,
    "noImplicitThis": true,
    "noUncheckedIndexedAccess": true
  }
}
```

2. **ESLint rules:**
```json
{
  "rules": {
    "@typescript-eslint/no-explicit-any": "error",
    "@typescript-eslint/no-unsafe-assignment": "error",
    "@typescript-eslint/prefer-nullish-coalescing": "error"
  }
}
```

3. **Type-first development:**
```typescript
// Define types first
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}

// Then implement
async function fetchUser(id: string): Promise<ApiResponse<User>> {
  // Implementation
}
```

4. **Use branded types for domain modeling:**
```typescript
type UserId = string & { readonly brand: unique symbol };
type Email = string & { readonly brand: unique symbol };

function createUserId(id: string): UserId {
  return id as UserId;
}
