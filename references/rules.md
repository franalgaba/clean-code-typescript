# Clean Code TypeScript — Full Ruleset

Reference for the `clean-code-ts` skill. Each section maps to a category in the review report. Rules include a short name (used in the report's "Rule" column), the principle, and a Bad → Good pattern.

---

## Table of Contents

1. [Variables](#variables)
2. [Functions](#functions)
3. [Objects and Data Structures](#objects-and-data-structures)
4. [Classes](#classes)
5. [SOLID](#solid)
6. [Testing](#testing)
7. [Concurrency](#concurrency)
8. [Error Handling](#error-handling)
9. [Formatting](#formatting)
10. [Comments](#comments)

---

## Variables

### Use meaningful variable names
Names should reveal intent. A reader should know what each variable represents without reading surrounding context.

Bad: `a1, a2, a3` → Good: `value, left, right`

### Use pronounceable variable names
If you can't say it out loud, it's a bad name.

Bad: `genymdhms`, `pszqint` → Good: `generationTimestamp`, `recordId`

### Use the same vocabulary for the same type of variable
Don't use `getUserInfo`, `getUserDetails`, and `getUserData` when they all return the same thing. Pick one: `getUser`.

### Use searchable names
Avoid magic numbers and unnamed constants. Extract them into named constants.

Bad: `setTimeout(restart, 86400000)` → Good: `const MILLISECONDS_PER_DAY = 24 * 60 * 60 * 1000; setTimeout(restart, MILLISECONDS_PER_DAY);`

### Use explanatory variables
Destructure to give meaningful names rather than using opaque keys.

Bad: `for (const keyValue of users)` → Good: `for (const [id, user] of users)`

### Avoid mental mapping
Explicit is better than implicit. Don't make readers decode single-letter names.

Bad: `const u = getUser()` → Good: `const user = getUser()`

### Don't add unneeded context
If the class/type already provides context, don't repeat it in property names.

Bad: `car.carMake, car.carModel` → Good: `car.make, car.model`

### Use default arguments instead of short circuiting
Default parameters are cleaner than `count !== undefined ? count : 10`.

Bad: `const loadCount = count !== undefined ? count : 10` → Good: `function loadPages(count: number = 10)`

### Use enum to document intent
When you care about distinctness rather than exact values, use `enum` instead of string-based objects.

---

## Functions

### Function arguments (2 or fewer ideally)
More than 2 arguments makes testing combinatorially harder. Use an options object with destructuring for 3+ parameters. This also enables named parameters and makes the signature self-documenting.

### Functions should do one thing
The most important rule. If a function does lookup + filter + side-effect, split it. Each function should have a single responsibility.

Bad: loop that looks up, checks active, then emails → Good: `clients.filter(isActiveClient).forEach(email)`

### Function names should say what they do
`addToDate` is ambiguous — adding what? → `addMonthToDate` is clear.

### Functions should only be one level of abstraction
Don't mix high-level orchestration with low-level details. Extract sub-operations into named functions.

### Remove duplicate code
If two functions share 80% of their logic, extract the common parts. But be thoughtful — bad abstractions can be worse than duplication.

### Set default objects with Object.assign or destructuring
Use spread / `Object.assign` / destructuring with defaults instead of manual `config.x = config.x || 'default'`.

### Don't use flags as function parameters
A boolean parameter usually means the function does two things. Split into two functions.

Bad: `createFile(name, temp: boolean)` → Good: `createFile(name)` + `createTempFile(name)`

### Avoid side effects (part 1)
A function that modifies external state is a side effect. Centralise side effects; keep most functions pure.

Bad: mutating a global `name` variable → Good: return a new value from a pure function.

### Avoid side effects (part 2)
Don't mutate input arrays/objects. Return new copies instead.

Bad: `cart.push(item)` → Good: `return [...cart, { item, date: Date.now() }]`

### Don't write to global functions
Never extend native prototypes (`Array.prototype.diff`). Use a class that extends the native instead.

### Favor functional programming over imperative
Prefer `map`, `filter`, `reduce` over manual `for` loops with mutation.

### Encapsulate conditionals
Extract complex boolean expressions into descriptively-named functions.

Bad: `if (subscription.isTrial || account.balance > 0)` → Good: `if (canActivateService(subscription, account))`

### Avoid negative conditionals
`isEmailUsed` with `!` is clearer than `isEmailNotUsed`.

### Avoid conditionals (use polymorphism)
Replace `switch` on type with polymorphic classes. Each subclass implements its own behaviour.

### Avoid type checking
Leverage TypeScript's type system instead of `instanceof` checks. Define a common interface method.

### Don't over-optimize
Don't cache `list.length` in modern environments. Trust the runtime.

### Remove dead code
Unused functions/imports belong in version history, not in the codebase.

### Use iterators and generators
For stream-like data, generators provide lazy execution and decouple producers from consumers.

---

## Objects and Data Structures

### Use getters and setters
Encapsulate access to enable validation, logging, lazy loading, and future-proof the API.

### Make objects have private/protected members
Use `private` and `readonly` to hide internals. Prefer `constructor(private readonly radius: number)` shorthand.

### Prefer immutability
Use `readonly`, `ReadonlyArray<T>`, and `as const` assertions. Immutable data prevents unexpected mutations.

### type vs. interface
Use `type` for unions/intersections, `interface` when you need `extends`/`implements`. Be consistent within a project.

---

## Classes

### Classes should be small
Measured by responsibility, not lines. Follow the Single Responsibility Principle.

### High cohesion and low coupling
Every field should ideally be used by most methods. If a class has two distinct groups of fields used by two distinct groups of methods, split it.

### Prefer composition over inheritance
Use inheritance only for true "is-a" relationships. For "has-a", compose objects.

### Use method chaining
Return `this` from builder-style methods for expressive, fluent APIs.

---

## SOLID

### Single Responsibility Principle (SRP)
A class should have only one reason to change. If a class handles both auth and settings, split it.

### Open/Closed Principle (OCP)
Open for extension, closed for modification. Use abstract base classes / interfaces so new behaviour can be added via new classes, not `if/else` chains.

### Liskov Substitution Principle (LSP)
Subclasses must be usable in place of their parent without breaking correctness. Classic violation: `Square extends Rectangle` where `setWidth` breaks `getArea` expectations.

### Interface Segregation Principle (ISP)
Clients shouldn't depend on methods they don't use. Split fat interfaces into small, focused ones.

### Dependency Inversion Principle (DIP)
Depend on abstractions (interfaces), not concretions. Inject dependencies rather than instantiating them internally.

---

## Testing

### The three laws of TDD
1. No production code unless it makes a failing test pass.
2. Write only enough of a test to fail.
3. Write only enough production code to pass.

### F.I.R.S.T. rules
Tests should be Fast, Independent, Repeatable, Self-Validating, and Timely (written before production code).

### Single concept per test
One assert per unit test. Don't bundle multiple scenarios.

### The name of the test should reveal its intention
Bad: `it('2/29/2020')` → Good: `it('should handle leap year')`

---

## Concurrency

### Prefer promises over callbacks
Callbacks cause nesting hell. Use `promisify` or native promises.

### Async/Await over promise chains
`async/await` is cleaner and more readable than `.then()` chains.

---

## Error Handling

### Always use Error for throwing or rejecting
Never `throw 'string'` or `Promise.reject('string')`. Use `new Error(...)` for stack traces.

Consider the `Result<R> | Failure<E>` pattern as a type-safe alternative to exceptions.

### Don't ignore caught errors
Never write an empty `catch` block or just `console.log`. Use a proper logger and handle the error.

### Don't ignore rejected promises
Same principle — always handle `.catch()` or use `try/catch` with `await`.

---

## Formatting

### Use consistent capitalization
- `PascalCase` for classes, interfaces, types, enums.
- `camelCase` for variables, functions, class members.
- `UPPER_SNAKE_CASE` for constants.

### Function callers and callees should be close
Keep calling functions above the functions they call. Read top-to-bottom like a newspaper.

### Organize imports
Group and alphabetize: polyfills → Node builtins → external → internal → parent → sibling. Use `import type` for type-only imports. Remove unused imports.

### Use TypeScript path aliases
Configure `baseUrl` and `paths` in `tsconfig.json` to avoid deep relative imports like `../../../services/UserService`.

---

## Comments

### Prefer self-explanatory code over comments
If you need a comment to explain what code does, the code isn't clear enough. Extract into a well-named variable or function.

Bad: `// Check if subscription is active` + `if (subscription.endDate > Date.now)` → Good: `const isSubscriptionActive = subscription.endDate > Date.now`

### Don't leave commented-out code
Delete it. Version control has history.

### Don't have journal comments
No changelogs in source files. Use `git log`.

### Avoid positional markers
No `//////////////// Section ////////////////` banners. Use proper code structure and IDE folding.

### TODO comments
Acceptable for noting future improvements — but they are not an excuse for leaving bad code.