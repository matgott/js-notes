#

---

- [lesson-90 - Javascript Engine](#lesson-90---javascript-engine)
  - [Runtime](#runtime)
- [lesson-92 - Scope](#lesson-92---scope)
  - [Block Scope](#block-scope)
  - [Function Scope/Local Scope](#function-scopelocal-scope)
  - [Module Scope](#module-scope)
  - [Global Scope](#global-scope)
  - [Lexical Scope](#lexical-scope)
- [lesson-94 - Hoisting](#lesson-94---hoisting)
- [lesson-91 - Execution Context](#lesson-91---execution-context)
  - [Execution Context Stack / Call Stack](#execution-context-stack--call-stack)
  - [Execution Context](#execution-context)
- [lesson-96 - The this keyword](#lesson-96---the-this-keyword)
- [lesson-99 - Primitives vs Object](#lesson-99---primitives-vs-object)
  - [Primitives](#primitives)
  - [Objects](#objects)
- [lesson-10 - Values and Variables](#lesson-10---values-and-variables)
- [lesson-12 - Data Types](#lesson-12---data-types)
- [lesson-13 - let, const and var](#lesson-13---let-const-and-var)
- [lesson-20 - Type Conversion and Coercion](#lesson-20---type-conversion-and-coercion)
- [lesson-21 - Truthy and Falsy Values](#lesson-21---truthy-and-falsy-values)
- [Nullish value](#nullish-value)
  - [Null vs Undefined](#null-vs-undefined)
- [lesson-22 - Equality Operators: == vs ===](#lesson-22---equality-operators--vs-)
- [lesson-27 - Statements & Expressions](#lesson-27---statements--expressions)
- [lesson-30 - JavaScript Releases](#lesson-30---javascript-releases)
- [lesson-32 - Strict Mode](#lesson-32---strict-mode)
- [lesson-33-34 - Functions](#lesson-33-34---functions)
- [lesson-103-104 - Destructuring](#lesson-103-104---destructuring)
  - [Array](#array)
  - [Objects](#objects-1)
- [lesson-105 - Spread Operator](#lesson-105---spread-operator)
- [lesson-106 - Rest Syntax](#lesson-106---rest-syntax)
- [lesson-107 - Short Circuiting (|| - &&)](#lesson-107---short-circuiting----)
  - [OR (||)](#or-)
  - [AND (&&)](#and-)
- [lesson-108 - Nullish Coalescing Operator (??)](#lesson-108---nullish-coalescing-operator-)
- [Optional Chaining (?)](#optional-chaining-)

---

## lesson-90 - Javascript Engine

Program or an interpreter which executes Javascript code. Can be an interpreter or `just-in-time` compiler.

- JIT: Code is converted into machine code at once, then executen immediately.

Steps:

- Parsing: Read the code and parsed into a data structure called `AST (abstract syntax tree)`.
  - Split the code into small pieces with meaning for the language and saving them into the `AST`.
  - Check for syntax errors.
- Compilation: Takes the generated `ASP` and compiles it to machine code.
- Execution in the `Call Stack`.
- Optimization: Some engines performs a unoptimized compilation for executing the code as fast is possible, then in background this code is beign optimized and recompiled. This can be done multiple times.

> Memory Heap: Is where the dynamic data (function, objects, variables definitions) will be stored. The Call Stack only has a reference to the HEAP.

#### Runtime

- Engine (Heap, Call Stack).
- Web Api (Browser) - C++ Bindings & Thread Pool (NodeJs).
- Callback Queue.
- Event Loop.

---

## lesson-92 - Scope

Where values and expressiones are "visibles" or can be referenced.
**Scope Chain:**

- Child scopes have access to parent scopes, but not vice versa.
- When a variable isn't in the current scope, the engine looks up in the scope chain. This is calling `variable lookup`.

#### Block Scope

Defines a scope for:

- `let` and `const` variables.
- `Class`
- `function` only in strict mode.

Example:

      if (true) {
        class Person {
            constructor(name) {
                this.name = name;
            }
        }

        const me = new Person("Matias")

        console.log(me); // output: Person { name: 'Matias' }
      }

      console.log(me); // ReferenceError: me is not defined
      console.log(new Person("Johana")); // ReferenceError: Person is not defined

#### Function Scope/Local Scope

Defines a scope for:

- `let`, `const` and `var` variables.
- `Class`
- `function`

Example:

      function test() {
          class Person {
              constructor(name, age) {
                  this.name = name;
                  this.age = age;
              }
          }

          const me = new Person("Matias", 25);

          console.log(me); // output: Person { name: 'Matias', age: 25 }

          function logNameAndAge() {
              console.log(`My name is ${me.name} and I'am ${me.age} years old`);
          }

          logNameAndAge(); // output: My name is Matias and I'am 25 years old
      }

      test();
      console.log(me); // ReferenceError: me is not defined
      console.log(new Person("Johana", 30)); // ReferenceError: Person is not defined
      logNameAndAge(); // ReferenceError: me is not defined

#### Module Scope

ES2015/ES6 module creates a scope for:

- `let`, `const` and `var` variables.
- `functions`
- `Class`

Example:

`module test.js`

      class Person {
          constructor(name, age) {
              this.name = name;
              this.age = age;
          }
      }

      function createMe() {
          return new Person("Matias", 25);
      }

      const me = createMe();
      var birthYear = new Date().getFullYear() - me.age;

      console.log(me); // output: Person { name: 'Matias', age: 25 }
      console.log(birthYear); // output: 1996

`module app.js`

      import "./test.js";

      console.log(me); // ReferenceError: me is not defined
      console.log(new Person("Johana", 30)); // ReferenceError: Person is not defined

#### Global Scope

Is the outermost scope. It is accesible from any inner (local) scope.

**Browser Enviroment:** The global scope is the topmost scope of .js file loadad using `<script>` tag (window object).

**Node Enviroment:** The top-level scope is not the global scope. Inside Node.js module a var declaration will be local to that module. It means: the global scope of a module is the module itself. Node has a `global object` that is accessible within the running process.

#### Lexical Scope

Defines how variable names are resolved in nested functions: inner functions contain the scope of parent functions

**References**

- [MDN Scope](https://developer.mozilla.org/en-US/docs/Glossary/Scope)
- [A Simple Explanation of Scope in JavaScript](https://dmitripavlutin.com/javascript-scope/)

---

## lesson-94 - Hoisting

Is a Javascript default behavior of moving all declarations to the top of the current scope. In case of variables only move the declaration, not the assignment.

This happens because at the moment of creating the execution context first all variables are declared.

- **Variables:** Are hoisted and initialized with `undefined`.
- **Functions:** Formal functions are hoisted and initialized with their function value.
- **let - const:** Are hoisted but the cannot be accesed before their declaration. This is called `Temporal dead zone` which start at the begining of the scope until the line where it is defined.

**References**

- [Are let and const hoisted?](https://dev.to/anku/are-let-and-const-hoisted-4kbi)

## lesson-91 - Execution Context

Is the enviroment in which the code is executed. [Refer to Scope](#scope)

Is evaluated as one of the following:

- Global code: Default enviroment where code is executed for the first time.
- Function code: Function scope.
- Eval code: Text to be executed inside the internal eval function. Call `eval` function generate a new `execution context`.

#### Execution Context Stack / Call Stack

Javascript works with a single thread, one one thing can ever happen at one time, other actions or events are queued in the `Execution Stack / Call Stack`

![Execution Stack](https://miro.medium.com/max/555/0*3Rt-SaR0slWsLg3w.jpg)

- First load `global execution context`.
- If function is called, new `execution context` is created and pushing to the top of the `execution stack`.
- If another function is called inside the current function, the same thing happens. A new `execution context` is created for the inner function that is pushed to the top of the `call stack`.
- Once the function completes the `execution context` it popped off the top of the stack returining control to the context below in the stack.

#### Execution Context

Every call to an `execution context` has 2 stages:

- ##### Creation Stage: When the function is called, but before executing it.
  - Create Scope Chain: `variable object` + all parent execution contexts.
  - Create `variable object`: arguments object, functions and variables properties.
  - Define `this` value.
  - Steps:
    - Initialize the `scope chain`.
    - Create the `variable object`.
    - Create the `arguments object`: Initialize the name and value and creates a reference copy.
    - Scan the context for function declarations: For each function creates a property in the `variable object` which has a reference pointer to the function in memory. If the function exists already, pointer value will be overwritten.
    - Scan the context for variable declarations: For each variable creates a property in the `variable object` and assign a memory address for it. Then initialize the value as `undefined` (or `uninitialized` for `let` and `const`). If the name already exists in the `variable object`, do nothin and continue scanning.
    - Determine the value of `this`.
- ##### Activation / Code Execution Stage: Run the code in the context and assign variable values as the code is executed line by line.

Example:

    function foo(i) {
        var a = 'hello';
        var b = function privateB() {};
        function c() {}
    }
    foo(22);

`Creation Stage`

    fooExecutionContext = {
        scopeChain: { ... },
        variableObject: {
            arguments: {
                0: 22,
                length: 1
            },
            i: 22,
            c: pointer to function c()
            a: undefined,
            b: undefined
        },
        this: { ... }
    }

`Activation Stage after function ends`

    fooExecutionContext = {
        scopeChain: { ... },
        variableObject: {
            arguments: {
                0: 22,
                length: 1
            },
            i: 22,
            c: pointer to function c()
            a: 'hello',
            b: pointer to function privateB()
        },
        this: { ... }
    }

**References**

- [What is the Execution Context & Stack in JavaScript?](https://medium.com/@itIsMadhavan/what-is-the-execution-context-stack-in-javascript-e169812e851a)

---

## lesson-96 - The this keyword

Special variable created for every execution context (function). Refers to the object who call the function.

`this` isn't static. It depends on how the function is called.

- Method: `this` = Object that is calling the method.

  Example:

      const me = {
          name: "Matias",
          year: 1995,
          calcAge() {
              return 2021 - `this.year`
          }
      }
      me.calcAge(); // 26

- Simple function call: `this` = undefined (strict mode) or global object.
- Arrow function: `this` = `this` of parent context: `lexical this`.
- Event listener: `this` = DOM element that the handler is attached to.

---

## lesson-99 - Primitives vs Object

#### Primitives

Primitive types are stored in the `call stack`, inside the execution context that they are declared.

When a primitive is declared a memory address is asigned to it, and save the value in that address. (This happen in the `Call Stack`).

If the variable primitive value changes, a new memory address is created for that value and memory will point to that new address.

#### Objects

All objects will get stored in the `Memory Heap`.
When an Object is declared it will be created in the `Heap` in a specific `address`.
In the `Call stack` an identifier is created pointing to the `Heap Address` that contains the object.
When a change is made on the object, that happen in the `Heap`, nothin change in the `Call Stack`.
If we create a new variable equal to an existing object an indentifier will be created in the `Call stack` pointing to the same `Heap Address` created before. For this reason if we made a change to this object through this identifier (variable) the same object in the `Heap` is modified, and both identifiers are pointing to the same `Heap Address`.

---

## lesson-10 - Values and Variables

Value: Smallest unit of information.

Variable: Container for a value. Their contained values can change.

- Naming standard: Camelcase.
- Naming rules:
  - Use latin characters (0-9, a-z, A-Z) and the underscore character.
  - Don't use underscores at the start of variable names.
  - Don't use numbers at the start of variable names.
  - Make variable names intuitive.
  - Variables are case sensitive myage <> myAge.
  - Don't use javascript reserve words.

**References**

- [MDN Variables](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/First_steps/Variables)

---

## lesson-12 - Data Types

Value: Is an object or a primitive data type.

- Primitives: Primitive value or primitive data type is data that is not an object and has no methods. All primitives values are immutables.

  > Immutability: Something that cannot change. A type is immutable when the 'state' of their value cannot change. Ex: `let age = 25` The state of the int `25` cannot been modified, 25 is 25 always.

  > Except for null and undefined, all primitive values have object equivalents that wrap around the primitive values

  > In contexts where a method is to be invoked on a primitive string or a property lookup occurs, JavaScript will automatically wrap the string primitive and call the method or perform the property lookup.

  - Number: Floating point numbers (decimals & integers).
  - String: Sequence of characters.
  - Boolean: Logical type (true or false)
  - Undefined: Value automatically assigned to declared variables without value.
  - Null (Structural Root): Represents a reference that points to a nonexistent or invalid address.
  - Symbol: Value that is unique and cannot be changed.
  - BigInt: Larger integers that the Number type can hold.

  > Javascript has dynamic typing: Data types are determined automatically by the value. Value has type, NOT variable

**References**

- [MDN Value](https://developer.mozilla.org/en-US/docs/Glossary/Value)
- [MDN Data Structures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)

---

## lesson-13 - let, const and var

**Scope:** The current context of execution. The context in which values and expressions are "visibles".

- **let:** Block-scope variable. Variable can be re assgined to different values.
- **const:** Block-scope constant. (Variable cannot reassign o re-declare). Initial values is required.
- **var:** Function-scope or Globally-scope variable.

**References**

- [MDN Scope](https://developer.mozilla.org/en-US/docs/Glossary/Scope)
- [MDN Let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)
- [MDN Const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)
- [MDN Var](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var)

---

## lesson-20 - Type Conversion and Coercion

Type Conversion: Manually convert from one type to another.

- `const year = "1991; console.log(Number(year) + 18); output: 2009`
- `console.log(Number("Jonas")); output: NaN (Not a Number, invalid number)`
- `console.log(String(23)); output: "23"`

Type Coercion: Javascript automatically convert behind the scenes for us.

> Plus operator: Convert to string.
>
> Minus, Multiplier, Divider, Logical operator: Convert to number.

- `console.log("I am " + 23 + " years old"); output: "I am 23 years old"`
- `console.log("23" - "10" - 3); output: 10`
- `console.log("23" \* "2"); output: 46`
- `console.log("24" / "2"); output: 12`
- `console.log("23" > "18"); output: true`

**References**

- [MDN Type Coercion](https://developer.mozilla.org/en-US/docs/Glossary/Type_coercion)
- [MDN Type Conversion](https://developer.mozilla.org/en-US/docs/Glossary/Type_Conversion)

---

## lesson-21 - Truthy and Falsy Values

**Truthy Values:** Values that are considered true in a Boolean context.

- Any value that isn't falsy

**Fasly values:** Values that are considered false in a Boolean context.

- 0, -0
- 0n, -0n (BigInt zero & negative zero)
- '', "", ``
- undefined
- null
- NaN

> Boolean context: Logical operators and conditionals.

**References**

- [MDN Truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)

---

## Nullish value

`null` or `undefined`

#### Null vs Undefined

`null` is an assigned value. It means `nothing`.

`undefined` typically means a variable has been declared but not defined.

Both are [primitives](#primitives) and [falsy values](#lesson-21---truthy-and-falsy-values)

    console.log(typeof null); // "object" (not "null" for legacy reasons)
    console.log(typeof undefined); // "undefined"
    console.log(null === undefined); // false
    console.log(null == undefined); // true
    console.log(null === null); // true
    console.log(null == null); // true
    console.log(!null); // true
    console.log(isNaN(1 + null)); // false
    console.log(isNaN(1 + undefined)); // true

---

## lesson-22 - Equality Operators: == vs ===

**Equality operator ==:** Checks whether its two oeprands are equal. It attempts to convert and compare operands that are of differentes types (Coercion).

**Strict equality operator ===:** Don't perform type coercion. Always considers operands of different types to be different.

- Operands are of different types: `false`. Example: `18 === "18" = false`
- Operands are objects: `true` if they refer to the same object (SAME not equal object).

  Examples

  `const obj = {name: "Matias"};`

  `obj === {name: "Matias"} = false`

  ***

  `const obj2 = obj;`

  `obj === obj2 = true`

- Operands are null or undefined: `true`.

  Examples:

  `null === null = true`

  `null === undefined = false`.

- Numbers must have same numeric values.
- Strings must have the same characters in the same order.
- Booleans must be both `true` or both `false`.

**References**

- [MDN Equality Operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Equality)
- [MDN Strict Equality Operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Strict_equality)

---

## lesson-27 - Statements & Expressions

**Expressions** Any unit of code that produce a value.
**Statements** Instruction to perform a spcific action.

- Expression statements: An expression on its own is also a statement
- Declaration statements: Create variables, define functions.
- Conditional statements: Execute statements based on the value of an expression.

> Template literal only allow expressions

**References**
[JavaScript Expressions and Statements](https://medium.com/launch-school/javascript-expressions-and-statements-4d32ac9c0e74#:~:text=Any%20unit%20of%20code%20that,has%20the%20following%20expression%20categories.)
[JavaScript Statements](https://flaviocopes.com/javascript-statements/)

---

## lesson-30 - JavaScript Releases

- ES5: Fully supported in all browsers.
- ES6/ES2015: Well support in all modern browsers, not support in olrder browsers.

Transpile and polyfill code (convert back to ES5 to ensure brwoser compatibility for all users):

- Transpile (source-to-source compilers): Take in a source code and convert it to another source code in some other language of similar level of abstraction or a different version of the same language. Ex: Babel
- Polyfill: Piece of code used to provide modern functionality on older browsers that don't natively support it
  - > First check if a browser support the feature, and use it if available, otherwise using their own implementation.

**References**

- [Wikipedia Polyfill](<https://en.wikipedia.org/wiki/Polyfill_(programming)>)
- [MDN Polyfill](https://developer.mozilla.org/en-US/docs/Glossary/Polyfill)

---

## lesson-32 - Strict Mode

`use strict`
Strict context prevents certain actions from beign tanken throwing errors.

Features:

- Prevent accidentally global variables creations.
- Prevent assignments which would fails. Example `NaN = 5` NaN is non-writable global variable.
- Prevent deletion of undeletable properties. Example `delete Object.prototype`
- Prevent duplicate function parameter names. Without strict mode, the last name hides privious one.
- Prevent settings properties on primitive values.
- Prevent duplicate property names.

**Reference**
[MDN Strict Mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)

---

## lesson-33-34 - Functions

Function parameter is in the declaration
Function argument is the value sent to the function parameter
Example:

`function calcAge(birthYear)` -> Parameters: `birthYear`

`calcAge(1995)` -> Argument: `1995`

- **Function expressions:**

  `const calcAge = function(birthYear) { return 2021 - birthYear}`

  - The function is assigned to the variable as their value. This function cannot be called before the assigment.

- **Function declaration:**

  `function calcAge(birthYear) {returh 2021 - birthYear}`

  - Function can be called before the declaration.

- **Arrow Function**: Impact alternative to a traditional function expression.

  - Dosn't have its own bindings to `this` or `super`, and should not be used as `methods`.
  - Not suitable for `call`, `apply` and `bind` methods.

    **References**: [MDN Arrow Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

---

## lesson-103-104 - Destructuring

Definition: Expression to unpack values from arrays, or properties from objects into distinct variables.

#### Array

Retrieve elements from the array and store them into variables.

    const arr = [2, 3, 4];
    const [a, b, c] = arr;
    console.log(a, b, c); // 2, 3, 4

Skip elements:

    const arr = [2, 3, 4];
    const [a, , b] = arr;
    console.log(a, b); // 2, 4

Invert elements:

    const arr = [2, 3, 4];
    let [a, b] = arr;
    console.log(a, b); // 2, 3

    [a, b] = [b, a]
    console.log(a, b); // 3, 2

Nested destructuring:

    const arr = [2, 3, 4, [5, 6]];
    const [a, , b, c] = arr;
    console.log(a, b, c); // 2, 4, [5,6]
    const [d, , e, [f, g]] = arr;
    console.log(d, e, f, g); // 2, 4, 5, 6

Default values:

    const arr = [8, 9];
    const [a, b, c] = arr;
    console.log(a, b, c); // 8, 9, undefined
    const [d, e = 1, f = 10] = arr;
    console.log(d, e, f); // 8, 9, 10

#### Objects

    const obj = {
        name: "Matias",
        lastname: "Caloia",
        address: {
            street: "Viamonte",
            number: 449,
            city: {
                country: "Argentina",
                state: "Santa Fe",
                city: "Rafaela"
            }
        },
    };

Retrieve properties from the object and store their values into variables.

    const {name, lastname, address} = obj;
    console.log(name, lastname, address); // Matias Caloia { street: 'Viamonte', number: 449 }

Change variable name:

    const { name: myName, lastname: myLastname, address: myLocation } = obj;
    console.log(myName, myLastname, myLocation); // Matias Caloia { street: 'Viamonte', number: 449 }

Default values:

    const { phoneNumbers = [], name: myName, lastname } = obj;
    console.log(phoneNumbers, myName, lastname); // [] Matias Caloia

Mutating variables:

    let name;
    let lastname;
    ({ name, lastname } = obj);
    console.log(name, lastname); // Matias Caloia

Nested objects:

    const {
        address: {street, number: streetNumber},
    } = obj;
    console.log(street, streetNumber); // Viamonte 449

---

## lesson-105 - Spread Operator

Allow us to expand an `Iterable: array, string, map, set` into all of its inidividual elements.

    const arr = [3, 4, 5];
    const newArr = [1, 2, ...arr];
    const newArr2 = [...arr, 1, 2];
    const newArr3 = [1, ...arr, 2];
    console.log(newArr, newArr2, newArr3); // [1, 2, 3, 4, 5] [3, 4, 5, 1, 2] [1, 3, 4, 5, 2]
    console.log(...newArr); // 1, 2, 3, 4, 5

Since ES2018/ES9 also works on Objects (Be careful, it's a SHALLOW copy)

    const obj = {
    name: "Matias",
    };
    const newObj = {...obj, lastname: "Caloia"};
    console.log(newObj); // { name: 'Matias', lastname: 'Caloia' }

---

## lesson-106 - Rest Syntax

Pack individual elements into an array

    function sum(...numbers) {
        return numbers.reduce((acc, current) => acc + current);
    }
    console.log(sum(3, 3, 4, 10, 5)); // 25

    const arr = [1, 2, 3, 4, 5];
    const [a, b, ...others] = arr;
    console.log(a, b, others); // 1, 2, [3, 4, 5]

---

## lesson-107 - Short Circuiting (|| - &&)

An expression with logical operators (|| - &&) is evaluated from left to right.

#### OR (||)

Return the first true value. And will reutrn the las `false` value if all the values are falsy.

    true || false
    > true

    console.log(undefined || "Matias" || "This will not evaluated"); // Matias

#### AND (&&)

Return `false` as soon as it gets any [falsy value](#lesson-21---truthy-and-falsy-values). And will reutrn the las `true` value if all the values are truthy.

    false && true
    > false

    true && "Matias" && "Caloia"
    > Caloia

    true && "" && "Matias"
    > false

---

## lesson-108 - Nullish Coalescing Operator (??)

Introduced in ES2020/ES9.

Logical operator that returns its right-hand side operand when its left-hand side operador is `null` or `undefined`.

    const name = null ?? "Matias";
    console.log(name); // Matias

    const lastname = undefined ?? "Caloia";
    console.log(lastname); // Caloia

    const age = 0 ?? 25;
    console.log(age); // 0

---

## Optional Chaining (?)

Check if the left reference is `nullish` and short-circuits with a return value of `undefined`.

    const obj = {
    info: {
        name: "Matias",
        lastname: "Caloia",
        phones: [{area: "3492", number: "311853"}],
    },
    sayName() {
        return `My name is ${this.info.name} ${this.info.lastname}`;
    },
    };

    console.log(obj.info?.name); // Matias
    console.log(obj.data?.lastname); // undefined
    console.log(obj.info.phones[0]?.area); // 3492
    console.log(obj.info.phones[1]?.area); // undefined
    console.log(obj.sayName?.() ?? "Method does not exist"); // My name is Matias Caloia
    console.log(obj.sayAge?.() ?? "Method does not exist"); //Method does not exist
