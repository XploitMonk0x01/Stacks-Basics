# TypeScript: From Basics to Advanced (Hands‑On With This Folder)

This guide teaches TypeScript end-to-end. It’s structured for quick skimming and hands-on practice.

## 1) Types and annotations

- Primitives: `string`, `number`, `boolean`, `null`, `undefined`, `bigint`, `symbol`.
- Type inference: TS infers from initializers. Prefer inference when it’s obvious.
- Explicit annotations: `let age: number = 18`.
- Any vs unknown
  - `any` disables safety. Avoid it in new code; use narrow types or generics.
  - `unknown` is safer; you must narrow before using.
- Special returns
  - `void`: no return value.
  - `never`: function doesn’t return (throws or infinite loop). See `myfunctions.ts` `handleError`.
- Nullability: enable with `strictNullChecks` (on by default in this repo’s tsconfig).

See: Official Handbook on the type system (https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)

Practice in this repo: `variableMe.ts`, `app.ts`.

Examples

```ts
// inference vs explicit
let greeting = "hello"; // string
// greeting = 42; // error
let age: number = 18;

// unknown is safer than any
function parseJSON(json: string): unknown {
  return JSON.parse(json);
}
const data: unknown = parseJSON('{"count":3}');
if (typeof data === "object" && data !== null && "count" in data) {
  // narrowed; safe to use
}

// void and never
function log(msg: string): void {
  console.log(msg);
}
function fail(message: string): never {
  throw new Error(message);
}
```

## 2) Arrays, tuples, enums

- Arrays: `string[]`, `Array<number>`; multi-d arrays `number[][]` (see `myArray.ts`).
- Tuples: fixed-length ordered types, e.g. `[string, number, boolean]` (see `myTupEnums.ts`).
- Enums: numeric or string enums (see `myEnums.ts`). Prefer union literals for libraries and public APIs for better tree-shaking.

Examples

```ts
// arrays
const names: string[] = ["john", "mike"];
const scores: Array<number> = [10, 20, 30];
const matrix: number[][] = [
  [1, 2],
  [3, 4],
];

// tuples (fixed order and length)
let userTuple: [string, number, boolean] = ["alice", 42, true];
// userTuple = [42, "alice", true]; // error (wrong order)

// enums (numeric by default)
enum SeatChoice {
  AISLE,
  MIDDLE,
  WINDOW,
}
const seat = SeatChoice.AISLE;

// Often prefer union literals for libraries:
type Theme = "dark" | "light";
```

## 3) Objects, type aliases, interfaces

- Type alias: `type User = { name: string; readonly id: string; email?: string }` (see `myObjects.ts`).
- Interface: open/mergeable contracts, can be extended (see `myInterface.ts`).
- Declaration merging: repeat `interface User { ... }` merges members (demonstrated in `myInterface.ts`).
- Intersections: `type A = B & C` (see `myObjects.ts`).
- Literal types: `'dark' | 'light'` (see `app.ts`).

When to use which:

- Use interface for object shapes intended to be extended/merged.
- Use type alias for unions, intersections, conditional/utility types.

Examples

```ts
// type alias
export type User = {
  readonly id: string;
  name: string;
  email?: string; // optional
};

// interface (can be merged/extended)
export interface Account {
  username: string;
}
export interface Account {
  // declaration merging
  createdAt: Date;
}

export interface AdminAccount extends Account {
  role: "admin" | "moderator";
}

// intersection
type Contact = { email: string } & { phone?: string };

// literal type
type Mode = "dark" | "light";
```

## 4) Functions

- Parameters: types, optional `?`, defaults, rest params.
- Return types: often inferred, but annotate on public APIs.
- Function types: `type Fn = (a: number, b: string) => void` (see `app.ts`).
- Overloads: declare multiple call signatures, provide one implementation.
- Contextual `this`: you can type `this` parameter in functions.
- Error handling: `never` for throwing functions (`handleError` in `myfunctions.ts`).

Examples

```ts
// parameter and return types
function multiply(a: number, b: number): number {
  return a * b;
}

// optional and default
function greet(name: string, title?: string) {
  return `Hello ${title ?? ""} ${name}`.trim();
}
function inc(x = 0) {
  return x + 1;
} // inferred types

// rest params
function sum(...nums: number[]): number {
  return nums.reduce((a, b) => a + b, 0);
}

// function type alias
type Stringify = (n: number) => string;
const toStr: Stringify = (n) => `${n}`;

// overloads
function len(x: string): number;
function len(x: any[]): number;
function len(x: string | any[]): number {
  return x.length;
}
```

Docs: https://www.typescriptlang.org/docs/handbook/2/functions.html

## 5) Unions, intersections, narrowing

- Unions: value is one of many types (see `myUnion.ts`).
- Narrowing strategies (see `pure ts/src/detection.ts`):

  - `typeof`, `in`, `instanceof` checks.
  - Discriminated unions via a common `kind` field.
  - Custom type predicates: `function isFish(pet: Fish | Bird): pet is Fish { ... }`.
  - Exhaustive checks with `never` in `switch` default.

  Examples

  ```ts
  type ID = number | string;

  function normalizeId(id: ID) {
    if (typeof id === "string") return id.toLowerCase();
    return id.toString();
  }

  // in-guard
  interface User {
    name: string;
  }
  interface Admin {
    name: string;
    isAdmin: true;
  }
  function isAdmin(u: User | Admin): u is Admin {
    return "isAdmin" in u;
  }

  // discriminated union
  interface Circle {
    kind: "circle";
    radius: number;
  }
  interface Square {
    kind: "square";
    side: number;
  }
  interface Rect {
    kind: "rect";
    w: number;
    h: number;
  }
  type Shape = Circle | Square | Rect;

  function area(s: Shape): number {
    switch (s.kind) {
      case "circle":
        return Math.PI * s.radius ** 2;
      case "square":
        return s.side * s.side;
      case "rect":
        return s.w * s.h;
      default: {
        const _exhaustive: never = s; // if a new variant is added, compiler warns here
        return _exhaustive;
      }
    }
  }
  ```

Docs: https://www.typescriptlang.org/docs/handbook/2/narrowing.html

## 6) Classes and OOP

- Access modifiers: `public`, `private`, `protected` (see `pure ts/src/index.ts`).
- `readonly` fields; computed getters/setters.
- `abstract` classes and methods (see `pure ts/src/abstractClass.ts`).
- Implementing interfaces and multiple interfaces (see `pure ts/src/second.ts`).
- `extends` and `super`; `override` (TS 4.3+) helps catch mistakes.

Examples

```ts
interface HasEmail {
  email: string;
}

class User implements HasEmail {
  public email: string;
  private _name: string;
  protected _score = 0;
  readonly createdAt = new Date();

  constructor(email: string, name: string) {
    this.email = email;
    this._name = name;
  }

  get name() {
    return this._name;
  }
  set name(v: string) {
    if (!v.trim()) throw new Error("Name required");
    this._name = v;
  }
}

class PremiumUser extends User {
  upgrade() {
    this._score += 10;
  } // allowed: protected
}

abstract class Storage {
  abstract save(key: string, value: string): void;
}

class MemoryStorage extends Storage {
  private data = new Map<string, string>();
  save(key: string, value: string) {
    this.data.set(key, value);
  }
}
```

Docs: https://www.typescriptlang.org/docs/handbook/2/classes.html

## 7) Generics (practical)

- Generic functions: `function identity<T>(v: T): T`.
- Constrained generics: `<T extends object>`; multiple type params (see `myGenerics.ts`).
- Defaults for type params: `<T = string>`.
- Generic classes/containers and methods (see `class Sellable<T>` in `myGenerics.ts`).
- Type-safe higher-order utilities: return and parameter inference.

Examples

```ts
// simple generic
function identity<T>(v: T): T {
  return v;
}

// constraint and default
interface HasId {
  id: string;
}
function getId<T extends HasId = HasId>(x: T): string {
  return x.id;
}

// generic container
class Box<T> {
  constructor(public value: T) {}
  map<U>(fn: (t: T) => U): Box<U> {
    return new Box(fn(this.value));
  }
}

// key-safe picker
function pick<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}
```

Docs: https://www.typescriptlang.org/docs/handbook/2/generics.html

## 8) Utility types you’ll actually use

- `Partial<T>`, `Required<T>`, `Readonly<T>`, `Pick<T,K>`, `Omit<T,K>`
- `Record<K,V>`, `NonNullable<T>`, `Exclude<T,U>`, `Extract<T,U>`
- `ReturnType<F>`, `Parameters<F>`, `ConstructorParameters<C>`, `InstanceType<C>`
- `Awaited<T>` for async results

Bonus patterns

- `keyof` and indexed access types: `T[K]`.
- Template literal types: `` `${Lowercase<S>}-${number}` ``.
- Mapped types: `{ [K in keyof T]?: T[K] }`.
- Conditional types: `T extends U ? X : Y` and `infer` for capturing types.

Examples

```ts
interface U {
  id: string;
  name: string;
  email?: string;
}

type UPartial = Partial<U>; // all optional
type URequired = Required<U>; // all required
const frozen: Readonly<U> = { id: "1", name: "A" }; // no mutation

// pick/omit
type UIdName = Pick<U, "id" | "name">;
type UNoEmail = Omit<U, "email">;

// record
type Roles = "admin" | "user";
const perms: Record<Roles, number> = { admin: 10, user: 1 };

// exclude/extract
type Primitives = string | number | boolean;
type NoBool = Exclude<Primitives, boolean>; // string | number

// function helpers
function foo(a: number, b: string) {
  return `${a}:${b}`;
}
type FooArgs = Parameters<typeof foo>; // [number, string]
type FooRet = ReturnType<typeof foo>; // string

// awaited
async function getNum() {
  return 42 as const;
}
type Num = Awaited<ReturnType<typeof getNum>>; // 42
```

Docs: https://www.typescriptlang.org/docs/handbook/utility-types.html

## 9) Modules and configuration

- Modules: ES Modules (`import/export`) vs CommonJS (`require/module.exports`). Your `pure ts/tsconfig.json` uses `"module": "commonjs"` and `"target": "es2016"`.
- `esModuleInterop`: enables default import compatibility with CommonJS. It’s `true` here.
- Output directory: `outDir` set to `./dist`.
- Strictness: `strict: true` enables the full suite of checks.
- Suggested extras to explore: `noUncheckedIndexedAccess`, `exactOptionalPropertyTypes`, `noImplicitOverride`.

Docs: https://www.typescriptlang.org/tsconfig

Example (ES module syntax)

```ts
// math.ts
export function add(a: number, b: number) {
  return a + b;
}
export const PI = 3.14;

// usage
import { add, PI } from "./math";
```

## 10) Decorators (modern note)

- TypeScript 5+ supports the new standard decorators syntax behind a flag. Legacy `experimentalDecorators` remains for older patterns. Prefer the standard decorators when targeting modern tooling.
- Use cases: class annotations, method/property decorators in frameworks like Angular and NestJS. Keep decorators minimal and testable.

Docs: https://www.typescriptlang.org/docs/handbook/decorators.html

Example (class + method decorator sketch)

```ts
function sealed<T extends { new (...args: any[]): {} }>(ctor: T) {
  Object.seal(ctor);
  Object.seal(ctor.prototype);
}

function log(target: any, key: string, descriptor: PropertyDescriptor) {
  const original = descriptor.value;
  descriptor.value = function (...args: unknown[]) {
    console.log(`calling ${key} with`, args);
    return original.apply(this, args);
  };
}

@sealed
class Service {
  @log
  fetch(id: string) {
    return { id };
  }
}
```

## 11) Working with JavaScript and libs

- JSDoc types on .js files: enable `"checkJs": true` to type-check JS.
- DefinitelyTyped type packages: `npm i -D @types/<lib>` when a library lacks types.
- DOM types: add relevant `"lib"` entries in tsconfig if you target browsers (e.g., `DOM`)

Docs: https://www.typescriptlang.org/docs/handbook/2/type-declarations.html

Example (JSDoc in a .js file to get type-checking)

```js
/**
 * @param {number[]} xs
 * @returns {number}
 */
export function average(xs) {
  return xs.reduce((a, b) => a + b, 0) / xs.length;
}
```

## 12) Common patterns and tips

- `as const` for literal inference: `const routes = ["/", "/about"] as const`.
- `satisfies` (TS 4.9+): `const theme = {mode: 'dark'} satisfies { mode: 'dark' | 'light' }` – checks shape without widening.
- Exhaustive `switch` with `never` to catch missing cases (see `detection.ts`).
- Prefer union literals over enums in libraries; use enums in app code where ergonomics matter.
- Avoid `any`. Reach for generics or `unknown` + narrowing.

Examples

```ts
// as const prevents widening
const routes = ["/", "/about"] as const; // type: readonly ["/", "/about"]

// satisfies checks shape without widening (TS 4.9+)
const theme = {
  mode: "dark",
  contrast: 1.2,
} satisfies { mode: "dark" | "light"; contrast: number };
```

## References

- TypeScript Handbook (modern, official): https://www.typescriptlang.org/docs/handbook/intro.html
- Everyday Types: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html
- Narrowing: https://www.typescriptlang.org/docs/handbook/2/narrowing.html
- Classes: https://www.typescriptlang.org/docs/handbook/2/classes.html
- Generics: https://www.typescriptlang.org/docs/handbook/2/generics.html
- Utility Types: https://www.typescriptlang.org/docs/handbook/utility-types.html
- TSConfig Reference: https://www.typescriptlang.org/tsconfig
- Playground to experiment: https://www.typescriptlang.org/play

---

If you want, we can turn this into a mini course with exercises and tests. Ask for “add tests” and I’ll scaffold Jest + ts-jest and a few unit tests around these files.
