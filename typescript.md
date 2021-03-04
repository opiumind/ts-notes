# TypeScript
(notes on https://www.typescriptlang.org/docs/handbook)

## Array

	`let list: number[] = [1, 2, 3];`
	or
	`let list: Array<number> = [1, 2, 3];`


## Tuple

	Tuple types allow you to express an array with a fixed number of elements whose types are known, but need not be the same.
	`let x: [string, number];`
	`x = ["hello", 10];`


## Enum

```
	enum Color {Red, Green, Blue}
	let c: Color = Color.Green;

	enum Color {Red = 1, Green, Blue} //By default, enums begin numbering their members starting at 0
	enum Color {Red = 1, Green = 2, Blue = 4}
```

## Type assertions (to tell the compiler “trust me, I know what I’m doing.”)

	`let someValue: any = "this is a string";`

	`let strLength: number = (<string>someValue).length;`
	or
	`let strLength: number = (someValue as string).length;`

//when using TypeScript with JSX, only as-style assertions are allowed


## Interfaces

	* Readonly properties
	* ReadonlyArray<T>
	*! Variables use const whereas properties use readonly

```
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any; // could also have any number of other properties
}
```
	* Interfaces are also capable of describing function types
	* Indexable Types (for index only string and number supported):
```
interface StringArray {
    [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```
```
interface NumberOrStringDictionary {
    [index: string]: number | string;
    length: number;    // ok, length is a number
    name: string;      // ok, name is a string
}
```
	* You can make index signatures readonly:
```
interface ReadonlyStringArray {
    readonly [index: number]: string;
}
```
	* Interfaces of Class Types:
```
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date): void;
}

class Clock implements ClockInterface {
    currentTime: Date = new Date();
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```
	* Interfaces can extend each other:
```
interface Square extends Shape {
    sideLength: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}
--------
class Animal {
    move(distanceInMeters: number = 0) {
        console.log(`Animal moved ${distanceInMeters}m.`);
    }
}

class Dog extends Animal {
    bark() {
        console.log('Woof! Woof!');
    }
}
```


## Classes

	* A constructor may also be marked protected. This means that the class cannot be instantiated outside of its containing class, but can be extended.
	* Readonly parameters
```
class Octopus {
    readonly numberOfLegs: number = 8;
    constructor(readonly name: string) {
    }
}
```
	* Getter, setter:
```
class Employee {
    private _fullName: string;

    get fullName(): string {
        return this._fullName;
    }

    set fullName(newName: string) {
        if (newName && newName.length > fullNameMaxLength) {
            throw new Error("fullName has a max length of " + fullNameMaxLength);
        }
        
        this._fullName = newName;
    }
}

let employee = new Employee();
employee.fullName = "Bob Smith";
```
	* Abstract classes:
```
abstract class Department {

    constructor(public name: string) {
    }

    printName(): void {
        console.log("Department name: " + this.name);
    }

    abstract printMeeting(): void; // must be implemented in derived classes
}

class AccountingDepartment extends Department {

    constructor() {
        super("Accounting and Auditing"); // constructors in derived classes must call super()
    }

    printMeeting(): void {
        console.log("The Accounting Department meets each Monday at 10am.");
    }

    generateReports(): void {
        console.log("Generating accounting reports...");
    }
}

let department: Department; // ok to create a reference to an abstract type
department = new Department(); // error: cannot create an instance of an abstract class
department = new AccountingDepartment(); // ok to create and assign a non-abstract subclass
department.printName();
department.printMeeting();
department.generateReports(); // error: method doesn't exist on declared abstract type
```
	* This variable will hold the class itself, or said another way its constructor function. Here we use typeof Greeter, that is “give me the type of the Greeter class itself” rather than the instance type. Or, more precisely, “give me the type of the symbol called Greeter,” which is the type of the constructor function. This type will contain all of the static members of Greeter along with the constructor that creates instances of the Greeter class:
```
let greeterMaker: typeof Greeter = Greeter;
greeterMaker.standardGreeting = "Hey there!";
```
	* Using a class as an interface:
```
class Point {
    x: number;
    y: number;
}

interface Point3d extends Point {
    z: number;
}

let point3d: Point3d = {x: 1, y: 2, z: 3};
```


## Functions	

	* Any optional parameters must follow required parameters
	* Default-initialized parameters don’t need to occur after required parameters, but then users need to explicitly pass undefined to get the default initialized value:
```
function buildName(firstName = "Will", lastName: string) {
    return firstName + " " + lastName;
}
let result4 = buildName(undefined, "Adams");
```
	* Rest parameters:
```
function buildName(firstName: string, ...restOfName: string[]) {
    return firstName + " " + restOfName.join(" ");
}

// employeeName will be "Joseph Samuel Lucas MacKinzie"
let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");

let buildNameFun: (fname: string, ...rest: string[]) => string = buildName;
```


## Generics

	* Generic Functions:
```
function identity<T>(arg: T): T {
    return arg;
}

let output = identity<string>("myString");
OR
let output = identity("myString");  // type of output will be 'string'
```
	* If you need get the length of arg use arrays of T rather than T:
```
function loggingIdentity<T>(arg: T[]): T[] {
    console.log(arg.length);  // Array has a .length, so no more error
    return arg;
}
```
	* Other ways to write:
```
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <U>(arg: U) => U = identity;
let myIdentity: {<T>(arg: T): T} = identity;
```
	* Generic Interfaces:
```
interface GenericIdentityFn<T> {
    (arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn<number> = identity;
```
	* It is not possible to create generic enums and namespaces
	* Generic Classes:
```
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```
	* Generic Constraints:
```
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // Now we know it has a .length property, so no more error
    return arg;
}

loggingIdentity({length: 10, value: 3});
```
	* Type Parameters in Generic Constraints:
```
function getProperty<T, K extends keyof T>(obj: T, key: K) {
    return obj[key];
}

let x = { a: 1, b: 2, c: 3, d: 4 };

getProperty(x, "a"); // okay
getProperty(x, "m"); // error: Argument of type 'm' isn't assignable to 'a' | 'b' | 'c' | 'd'.
```


## Enums
	
	* Numeric Enums:
```
enum Direction {
    Up = 1,
    Down,
    Left,
    Right,
}
// All of the following members are auto-incremented from that point on

enum Direction {
    Up,
    Down,
    Left,
    Right,
}
// Here, Up would have the value 0, Down would have 1, etc
```
	* Numeric enums can be mixed in computed and constant members:
```
enum E {
    A = getSomeValue(),
    B, // Error! Enum member must have initializer.
}
```
	* In a string enum, each member has to be constant-initialized with a string literal, or with another string enum member.
	* Heterogeneous enums. Technically enums can be mixed with string and numeric members, but it’s not clear why you would ever want to do so, don't do so.
	* An enum member is considered constant if:
		- It is the first member in the enum and it has no initializer, in which case it’s assigned the value 0
		- It does not have an initializer and the preceding enum member was a numeric constant. In this case the value of the current enum member will be the value of the preceding enum member plus one
		- The enum member is initialized with a constant enum expression
		- It is a compile time error for constant enum expressions to be evaluated to NaN or Infinity
	* In all other cases enum member is considered computed
	* keyof with enums:
```
enum LogLevel {
    ERROR, WARN, INFO, DEBUG
}

/**
 * This is equivalent to:
 * type LogLevelStrings = 'ERROR' | 'WARN' | 'INFO' | 'DEBUG';
 */
type LogLevelStrings = keyof typeof LogLevel;

function printImportant(key: LogLevelStrings, message: string) {
    const num = LogLevel[key];
    if (num <= LogLevel.WARN) {
       console.log('Log level key is: ', key);
       console.log('Log level value is: ', num);
       console.log('Log level message is: ', message);
    }
}
printImportant('ERROR', 'This is a message');
``` 
	* Reverse mappings. Numeric enums members also get a reverse mapping from enum values to enum names (!string enum members do not get a reverse mapping):
```
enum Enum {
    A
}
let a = Enum.A;
let nameOfA = Enum[a]; // "A"
```
	* Const enums:
```
const enum Enum {
    A = 1,
    B = A * 2
}
```
//Const enums can only use constant enum expressions.

	* Ambient enums. Ambient enums are used to describe the shape of already existing enum types:
```
declare enum Enum {
    A = 1,
    B,
    C = 2
}
```


## Type Compatibility

	* Type compatibility in TypeScript is based on structural subtyping (!!!TypeScript is a structural type system!!!)
    * In TypeScript, there are two kinds of compatibility: subtype and assignment. (Different places in the language use one of the two compatibility mechanisms)
	* nominally-typed languages: C# or Java will give errors in typescript code like:
```
interface Named {
  name: string;
}

class Person {
  name: string;
}

let p: Named;
// OK, because of structural typing
p = new Person();
```
    ...because the Person class does not explicitly describe itself as being an implementer of the Named interface. TypeScript’s structural type system was designed based on how JavaScript code is typically written. Because JavaScript widely uses anonymous objects like function expressions and object literals, it’s much more natural to represent the kinds of relationships found in JavaScript libraries with a structural type system instead of a nominal one.

	* The basic rule for TypeScript’s structural type system is that x is compatible with y if y has at least the same members as x. For example:

```
interface Named {
    name: string;
}

let x: Named;
// y's inferred type is { name: string; location: string; }
let y = { name: "Alice", location: "Seattle" };
x = y; // (less properties)=(more properties)!!!
```

    * Comparing functions:
```
// Functions
let x = (a: number) => 0;
let y = (b: number, s: string) => 0;

y = x; // OK (more parameters)=(less parameters)!!!
x = y; // Error
```
    * But return types are treated different:
```
let x = () => ({name: "Alice"});
let y = () => ({name: "Alice", location: "Seattle"});

x = y; // OK (less properties)=(more properties)!!!
y = x; // Error, because x() lacks a location property
```
    * Enums are compatible with numbers, and numbers are compatible with enums.
    * Enum values from different enum types are considered incompatible:
```
enum Status {
  Ready,
  Waiting,
}
enum Color {
  Red,
  Blue,
  Green,
}

let status = Status.Ready;
status = Color.Green; // Error
```
    * Private and protected members in a class affect their compatibility. For example, if the target type contains a private member, then the source type must also contain a private member that originated from the same class.
    * Generics:
```
interface Empty<T> {}
let x: Empty<number>;
let y: Empty<string>;

x = y; // OK, because y matches structure of x
```
But:
```
interface NotEmpty<T> {
  data: T;
}
let x: NotEmpty<number>;
let y: NotEmpty<string>;

x = y; // Error, because x and y are not compatible
```
And for generic types that do not have their type arguments specified, compatibility is checked by specifying any in place of all unspecified type arguments:
```
let identity = function <T>(x: T): T {
  // ...
};

let reverse = function <U>(y: U): U {
  // ...
};

identity = reverse; // OK, because (x: any) => any matches (y: any) => any
```
    * Table of type compatibility https://www.typescriptlang.org/docs/handbook/type-compatibility.html
        - Everything is assignable to itself.
        - any and unknown are the same in terms of what is assignable to them, different in that unknown is not assignable to anything except any.
        - unknown and never are like inverses of each other. Everything is assignable to unknown, never is assignable to everything. Nothing is assignable to never, unknown is not assignable to anything (except any).
        - void is not assignable to or from anything, with the following exceptions: any, unknown, never, undefined, and null


## Advanced Types

    * <Type guard> is some expression that performs a runtime check that guarantees the type in some scope.
To define a type guard, we simply need to define a function whose return type is a type predicate:
```
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}
```

`pet is Fish` is our type predicate in this example. A predicate takes the form `parameterName is Type`, where parameterName must be the name of a parameter from the current function signature.

```
// Both calls to 'swim' and 'fly' are now okay.
let pet = getSmallPet();

if (isFish(pet)) {
  pet.swim();
} else {
  pet.fly();
}
```
    * You may use the type guard isFish to filter an array of Fish | Bird and obtain an array of Fish:
```
const zoo: (Fish | Bird)[] = [getSmallPet(), getSmallPet(), getSmallPet()];
const underWater1: Fish[] = zoo.filter(isFish);
// or, equivalently
const underWater2: Fish[] = zoo.filter<Fish>(isFish);
const underWater3: Fish[] = zoo.filter<Fish>(pet => isFish(pet));
```
    * The in operator also acts as a narrowing expression for types:
```
function move(pet: Fish | Bird) {
  if ("swim" in pet) {
    return pet.swim();
  }
  return pet.fly();
}
```
    * By default, the type checker considers null and undefined assignable to anything. But the `--strictNullChecks` flag fixes this: when you declare a variable, it doesn’t automatically include null or undefined. With `--strictNullChecks`, an optional parameter automatically adds | undefined (For example, 'number | undefined'). The same is true for optional properties.
    * The syntax is postfix !: identifier! removes null and undefined from the type of identifier:
```
user!.email!.length;
```
    * <Type aliases> create a new name for a type (can also be generic):
```
type Second = number;

let timeInSecond: number = 10;
let time: Second = 10;

type Container<T> = { value: T };
```
    * We can also have a type alias refer to itself in a property.
    * A type cannot be re-opened to add new properties vs an interface which is always extendable.
    * If you can’t express some shape with an interface and you need to use a union or tuple type, type aliases are usually the way to go, otherwise interfaces are better.
    * If the class uses this types, you can extend it and the new class can use the old methods with no changes:
```
class BasicCalculator {
  public constructor(protected value: number = 0) {}
  public currentValue(): number {
    return this.value;
  }
  ...
}

class ScientificCalculator extends BasicCalculator {
  public constructor(value = 0) {
    super(value);
  }
  public sin() {
    this.value = Math.sin(this.value);
    return this;
  }
}
```
    * Union type: (string | number)


## Modules

    * Modules are executed within their own scope, not in the global scope
    * The relationships between modules are specified in terms of imports and exports at the file level
    * Any declaration (such as a variable, function, class, type alias, or interface) can be exported by adding the `export` keyword:
```
export interface StringValidator {
  isAcceptable(s: string): boolean;
}
```
    * Exports can be renamed: `export { ZipCodeValidator as mainValidator };`
    * Re-exports. It does not import it locally, or introduce a local variable:
```
export class ParseIntBasedZipCodeValidator {
  isAcceptable(s: string) {
    return s.length === 5 && parseInt(s).toString() === s;
  }
}

// Export original validator but rename it
export { ZipCodeValidator as RegExpBasedZipCodeValidator } from "./ZipCodeValidator";
```
    * Optionally, a module can wrap one or more modules and combine all their exports using export * from "module" syntax:
```
export * from "./StringValidator"; // exports 'StringValidator' interface
export * from "./ZipCodeValidator"; // exports 'ZipCodeValidator' class and 'numberRegexp' constant value
export * from "./ParseIntBasedZipCodeValidator"; //  exports the 'ParseIntBasedZipCodeValidator' class
// and re-exports 'RegExpBasedZipCodeValidator' as alias
// of the 'ZipCodeValidator' class from 'ZipCodeValidator.ts'
// module.
```
    * Imports:
```
import { ZipCodeValidator } from "./ZipCodeValidator";

let myValidator = new ZipCodeValidator();

// Can be also renamed:
import { ZipCodeValidator as ZCV } from "./ZipCodeValidator";
```
    * Import the entire module into a single variable, and use it to access the module exports:
```
import * as validator from "./ZipCodeValidator";
let myValidator = new validator.ZipCodeValidator();
```
    * Importing Types:
```
import { APIResponseType } from "./api";

// Explicitly use import type
import type { APIResponseType } from "./api";
```
    * Default exports:
```
//ZipCodeValidator.ts

export default class ZipCodeValidator {
    ...
}

//Test.ts

import validator from "./ZipCodeValidator";

let myValidator = new validator();
```
    * Default exports can also be just values or functions:
```
export default "123";

export default function (s: string) {
  return s.length === 5 && numberRegexp.test(s);
}
```
    * Export all as x, a shorthand for re-exporting another module with a name:
```
export * as utilities from "./utilities";

import { utilities } from "./index";
```
    * The `export =` syntax specifies a single object that is exported from the module:
```
class ZipCodeValidator {
  ...
  }
}
export = ZipCodeValidator;

import zip = require("./ZipCodeValidator");
```
    * In Type Script we only can load a module under some conditions (different syntax in different libraries: Node.js, require.js, System.js, etc.)
    * To describe the shape of libraries not written in TypeScript, we need to declare the API that the library exposes: in .d.ts files. See https://www.typescriptlang.org/docs/handbook/modules.html
    * Export as close to top-level as possible:
        - If you’re only exporting a single class or function, use export default:
```
//MyClass.ts
export default class SomeType {
  constructor() { ... }
}

//Consumer.ts
import t from "./MyClass";
import f from "./MyFunc";
let x = new t();
console.log(f());
```
        - If you’re exporting multiple objects, put them all at top-level:
```
//MyThings.ts
export class SomeType {
  /* ... */
}
export function someFunc() {
  /* ... */
}
```
        - Explicitly list imported names:
```
import { SomeType, someFunc } from "./MyThings";
let x = new SomeType();
let y = someFunc();
```
        - Use the namespace import pattern if you’re importing a large number of things
```
//MyLargeModule.ts
export class Dog { ... }
export class Cat { ... }
export class Tree { ... }
export class Flower { ... }

//Consumer.ts
import * as myLargeModule from "./MyLargeModule.ts";
let x = new myLargeModule.Dog();
```


## Namespaces

    * Name classes like `Validation.LettersOnlyValidator`, where "Validation" is a name of the namespace (internal module):
```
namespace Validation {
  export interface StringValidator {
    isAcceptable(s: string): boolean;
  }

  export class LettersOnlyValidator implements StringValidator {
    ...
    }
  }

  export class ZipCodeValidator implements StringValidator {
    ...
  }
}
```
    * You can store namespace and classes declarations in different files:
```
//Validation.ts
namespace Validation {
  export interface StringValidator {
    isAcceptable(s: string): boolean;
  }
}

//LettersOnlyValidator.ts
/// `<reference path="Validation.ts" />`
namespace Validation {
  const lettersRegexp = /^[A-Za-z]+$/;
  export class LettersOnlyValidator implements StringValidator {
    isAcceptable(s: string) {
      return lettersRegexp.test(s);
    }
  }
}

//Test.ts
// Validators to use
let validators: { [s: string]: Validation.StringValidator } = {};
validators["Letters only"] = new Validation.LettersOnlyValidator();
```
    * We can use concatenated output using the `--outFile` flag to compile all of the input files into a single JavaScript output file:
```
tsc --outFile sample.js Test.ts
or
tsc --outFile sample.js Validation.ts LettersOnlyValidator.ts ZipCodeValidator.ts Test.ts
//or in `<script>`
```
    * Using aliases:
```
import polygons = Shapes.Polygons;
let sq = new polygons.Square(); // Same as 'new Shapes.Polygons.Square()'
```
    * Ambient namespaces: see https://www.typescriptlang.org/docs/handbook/namespaces.html#table-of-contents


## Namespaces and Modules

    * Modules can contain both code and declarations, provide for better code reuse, stronger isolation and better tooling support for bundling. Recommended!!!
    * Namespaces are simply named JavaScript objects in the global namespace, can span multiple files, and can be concatenated using `--outFile`


## Module Resolution

    * Module resolution is the process the compiler uses to figure out what an import refers to
    * A relative import is one that starts with /, ./ or ../
    * Any other import is considered non-relative:
```
import * as $ from "jquery";
import { Component } from "@angular/core";
```
    * You should use relative imports for your own modules that are guaranteed to maintain their relative location at runtime
    * A non-relative import can be resolved relative to baseUrl, or through path mapping
    * There are two possible module resolution strategies: Node and Classic. 
    * You can use the `--moduleResolution` flag to specify the module resolution strategy. If not specified, the default is Node for `--module commonjs`, and Classic otherwise (including when `--module` is set to amd, system, umd, es2015, esnext, etc.).
    * Classic (`import { b } from "./moduleB"`) used to be TypeScript’s default resolution strategy. For non-relative it will search all the way up from the current folder of the file with the import. For relative by standard usual way. 
    * Node resolution (`var x = require("./moduleB");`) for relative resolution: file name -> package.json in the folder -> index.js in the folder 
    * Node resolution for non-relative resolution: all steps above in each directory all the way up
    * Additional module resolution flags:
        - "baseUrl", informs the compiler where to find modules. Given path is relative, it is computed based on current directory.
        - "paths", ("baseUrl" must be specified for this flag, "paths" are resolved relative to "baseUrl") location for specific modules:
```
{
  "compilerOptions": {
    "baseUrl": ".", // This must be specified if "paths" is.
    "paths": {
      "jquery": ["node_modules/jquery/dist/jquery"] // This mapping is relative to "baseUrl"
    }
  }
}
```
        - "rootDirs", specify a list of roots whose contents are expected to merge at run-time:
```
"rootDirs": ["src/views", "generated/templates/views"]
```
    * Run `tsc --traceResolution` invoking the compiler to see the log
    * `tsc app.ts moduleA.ts --noResolve` instructs the compiler not to “add” any files to the compilation that were not passed on the command line


## Declaration Merging

    * Is a TypeScript concept
    * Means that the compiler merges separate declarations declared with the same name into a single definition
    * All create Namespace, Type and Value:
        - Namespace: N, V
        - Class: T, V
        - Enum: T, V
        - Interface: T
        - Type Alias: T
        - Function: V
        - Variable: V
    * Merging Interfaces (the second interface will have a higher precedence than the first):
```
interface Box {
  height: number;
  width: number;
}

interface Box {
  scale: number;
}

let box: Box = { height: 5, width: 6, scale: 10 };
```
    * Merging Namespaces. All exported members will be merged
    * Merging Namespaces with Classes: see https://www.typescriptlang.org/docs/handbook/declaration-merging.html. Similarly, namespaces can be used to extend enums with static members
    

## JSX

    * Is an embeddable XML-like syntax. It is meant to be transformed into valid JavaScript
    * In order to use JSX you must do two things.
        1) Name your files with a .tsx extension
        2) Enable the jsx option
    * TypeScript ships with three JSX modes: preserve, react, and react-native
        - preserve mode will keep the JSX as part of the output to be further consumed by another transform step (e.g. Babel)
        - react mode will emit React.createElement, does not need to go through a JSX transformation before use, and the output will have a .js file
        - react-native mode is the equivalent of preserve in that it keeps all JSX, but the output will instead have a .js file
    * You can specify this mode using either the `--jsx` command line flag or the corresponding option jsx in your tsconfig.json
    * In .tsx you can't use angle bracket type assertions (`var foo = <foo>bar;`). Instead use `var foo = bar as foo;`
    * An intrinsic element (e.g. a div or span in a DOM environment) always begins with a lowercase letter, and a value-based element always begins with an uppercase letter.
    * JSX.IntrinsicElements interface:
```
declare namespace JSX {
  interface IntrinsicElements {
    foo: any;
  }
}

<foo />; // ok
<bar />; // error, since it has not been specified on JSX.IntrinsicElements
```
    * The component is defined as a JavaScript function where its first argument is a props object, and return type must be assignable to JSX.Element:
```
function ComponentFoo(prop: FooProp) {
  return <AnotherComponent name={prop.name} />;
}
```
    * JSX allows you to embed expressions between tags by surrounding the expressions with curly braces ({ }).


## Decorators

    * An experimental feature of TypeScript, may change
    * To enable experimental support for decorators, you must enable the experimentalDecorators compiler option either on the command line or in your tsconfig.json


## Mixins

    * Another popular way of building up classes from reusable components is to build them by combining simpler partial classes
    * The pattern relies on using Generics with class inheritance to extend a base class.


## Triple-Slash Directives
    
    * Are single-line comments containing a single XML tag. The contents of the comment are used as compiler directives.
    * Are only valid at the top of their containing file
    * `/// <reference path="..." />` - serves as a declaration of dependency between files.
    * `/// <reference types="..." />` - directive declares a dependency on a package. An easy way to think of triple-slash-reference-types directives are as an import for declaration packages.
    * `/// <reference lib="..." />` - allows a file to explicitly include an existing built-in lib file
    * `/// <reference no-default-lib="true"/>` - marks a file as a default library (this comment at the top of lib.d.ts). Note that when passing --skipDefaultLibCheck, the compiler will only skip checking files with /// <reference no-default-lib="true"/>.
    * `/// <amd-module />` - allows passing an optional module name to the compiler (by default AMD modules are generated anonymous):
```
///<amd-module name="NamedModule"/>
export class C {}
```


## Type Checking JavaScript Files (some notable differences on how checking works in .js files compared to .ts files)

    * No new members can be added that were not specified in the original literal in .ts


## Utility Types

    * Partial<Type> - Constructs a type with all properties of Type set to optional:
```
interface Todo {
  title: string;
  description: string;
}

function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>) {
  return { ...todo, ...fieldsToUpdate };
}

const todo1 = {
  title: "organize desk",
  description: "clear clutter",
};

const todo2 = updateTodo(todo1, {
  description: "throw out trash",
});
```
    * Readonly<Type> - Constructs a type with all properties of Type set to readonly:
```
interface Todo {
  title: string;
}

const todo: Readonly<Todo> = {
  title: "Delete inactive users",
};

todo.title = "Hello";
// Cannot assign to 'title' because it is a read-only property.
```
    * Record<Keys,Type> - Constructs an object type whose property keys are Keys and whose property values are Type:
```
interface PageInfo {
  title: string;
}

type Page = "home" | "about" | "contact";

const nav: Record<Page, PageInfo> = {
  about: { title: "about" },
  contact: { title: "contact" },
  home: { title: "home" },
};

nav.about;
// ^ = const nav: Record
```
    * Pick<Type, Keys> - Constructs a type by picking the set of properties Keys from Type:
```
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = Pick<Todo, "title" | "completed">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};

todo;
// ^ = const todo: TodoPreview
```
    * Omit<Type, Keys> - Constructs a type by picking all properties from Type and then removing Keys:
```
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = Omit<Todo, "description">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};

todo;
// ^ = const todo: TodoPreview
```
    * Exclude<Type, ExcludedUnion> - Constructs a type by excluding from Type all union members that are assignable to ExcludedUnion:
```
type T0 = Exclude<"a" | "b" | "c", "a">;
//    ^ = type T0 = "b" | "c"
type T1 = Exclude<"a" | "b" | "c", "a" | "b">;
//    ^ = type T1 = "c"
type T2 = Exclude<string | number | (() => void), Function>;
//    ^ = type T2 = string | number
```
    * Extract<Type, Union> - Constructs a type by extracting from Type all union members that are assignable to Union:
```
type T0 = Extract<"a" | "b" | "c", "a" | "f">;
//    ^ = type T0 = "a"
type T1 = Extract<string | number | (() => void), Function>;
//    ^ = type T1 = () => void
```
    * NonNullable<Type> - Constructs a type by excluding null and undefined from Type:
```
type T0 = NonNullable<string | number | undefined>;
//    ^ = type T0 = string | number
type T1 = NonNullable<string[] | null | undefined>;
//    ^ = type T1 = string[]
```
    * Parameters<Type> - Constructs a tuple type from the types used in the parameters of a function type Type
```
declare function f1(arg: { a: number; b: string }): void;

type T0 = Parameters<() => string>;
//    ^ = type T0 = []
type T1 = Parameters<(s: string) => void>;
//    ^ = type T1 = [s: string]
type T2 = Parameters<<T>(arg: T) => T>;
//    ^ = type T2 = [arg: unknown]
type T3 = Parameters<typeof f1>;
//    ^ = type T3 = [arg: {
//        a: number;
//        b: string;
//    }]
type T4 = Parameters<any>;
//    ^ = type T4 = unknown[]
type T5 = Parameters<never>;
//    ^ = type T5 = never
type T6 = Parameters<string>;
Type 'string' does not satisfy the constraint '(...args: any) => any'.
//    ^ = type T6 = never
type T7 = Parameters<Function>;
Type 'Function' does not satisfy the constraint '(...args: any) => any'.
  Type 'Function' provides no match for the signature '(...args: any): any'.
//    ^ = type T7 = never
```
    * ConstructorParameters<Type> - produces a tuple type with all the parameter types (or the type never if Type is not a function):
```
type T0 = ConstructorParameters<ErrorConstructor>;
//    ^ = type T0 = [message?: string]
type T1 = ConstructorParameters<FunctionConstructor>;
//    ^ = type T1 = string[]
type T2 = ConstructorParameters<RegExpConstructor>;
//    ^ = type T2 = [pattern: string | RegExp, flags?: string]
type T3 = ConstructorParameters<any>;
//    ^ = type T3 = unknown[]

type T4 = ConstructorParameters<Function>;
Type 'Function' does not satisfy the constraint 'new (...args: any) => any'.
  Type 'Function' provides no match for the signature 'new (...args: any): any'.
//    ^ = type T4 = never
```
    * ReturnType<Type> - Constructs a type consisting of the return type of function Type:
```
declare function f1(): { a: number; b: string };

type T0 = ReturnType<() => string>;
//    ^ = type T0 = string
type T1 = ReturnType<(s: string) => void>;
//    ^ = type T1 = void
type T2 = ReturnType<<T>() => T>;
//    ^ = type T2 = unknown
type T3 = ReturnType<<T extends U, U extends number[]>() => T>;
//    ^ = type T3 = number[]
type T4 = ReturnType<typeof f1>;
//    ^ = type T4 = {
//        a: number;
//        b: string;
//    }
type T5 = ReturnType<any>;
//    ^ = type T5 = any
type T6 = ReturnType<never>;
//    ^ = type T6 = never
type T7 = ReturnType<string>;
Type 'string' does not satisfy the constraint '(...args: any) => any'.
//    ^ = type T7 = any
type T8 = ReturnType<Function>;
Type 'Function' does not satisfy the constraint '(...args: any) => any'.
  Type 'Function' provides no match for the signature '(...args: any): any'.
//    ^ = type T8 = any
```
    * InstanceType<Type> - Constructs a type consisting of the instance type of a constructor function in Type:
```
class C {
  x = 0;
  y = 0;
}

type T0 = InstanceType<typeof C>;
//    ^ = type T0 = C
type T1 = InstanceType<any>;
//    ^ = type T1 = any
type T2 = InstanceType<never>;
//    ^ = type T2 = never
type T3 = InstanceType<string>;
Type 'string' does not satisfy the constraint 'new (...args: any) => any'.
//    ^ = type T3 = any
type T4 = InstanceType<Function>;
Type 'Function' does not satisfy the constraint 'new (...args: any) => any'.
  Type 'Function' provides no match for the signature 'new (...args: any): any'.
//    ^ = type T4 = any
```
    * Required<Type> - Constructs a type consisting of all properties of Type set to required. The opposite of Partial:
```
interface Props {
  a?: number;
  b?: string;
}

const obj: Props = { a: 5 };

const obj2: Required<Props> = { a: 5 };
Property 'b' is missing in type '{ a: number; }' but required in type 'Required<Props>'.
```
    * ThisParameterType<Type> - Extracts the type of the this parameter for a function type, or unknown if the function type has no this parameter:
```
function toHex(this: Number) {
  return this.toString(16);
}

function numberToString(n: ThisParameterType<typeof toHex>) {
  return toHex.apply(n);
}
```
    * OmitThisParameter<Type> - Removes the this parameter from Type. If Type has no explicitly declared this parameter, the result is simply Type:
```
function toHex(this: Number) {
  return this.toString(16);
}

const fiveToHex: OmitThisParameter<typeof toHex> = toHex.bind(5);

console.log(fiveToHex());
```
    * ThisType<Type> - This utility does not return a transformed type. Instead, it serves as a marker for a contextual this type. Note that the --noImplicitThis flag must be enabled to use this utility
