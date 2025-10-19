# The Complete HTML, CSS, and JavaScript Learning Book

This step-by-step guide teaches modern web fundamentals using your own notes and examples. It starts with HTML basics, layers on CSS, and then moves through JavaScript fundamentals, DOM manipulation, events, array methods, and OOP—as reflected in your `Web Dev/Notes.txt` and `Web Dev/JS Basic` folder.

## Table of Contents

1. HTML Essentials
2. CSS Fundamentals
3. Layout: Flexbox and Grid
4. Positioning and the Box Model
5. Icons, Media, and Tailwind Notes
6. JavaScript Basics
7. DOM Manipulation
8. Events and Interactions
9. Arrays and Iteration Methods
10. Objects, Maps, and Reduce Patterns
11. Modules and Script Loading
12. Small DOM Mini-Projects
13. JavaScript OOP (Classes, Prototypes, Inheritance)
14. More JavaScript Language Features
15. Timers and Async (Callbacks, Promises, Async/Await)
16. Next Steps

---

## 1) HTML Essentials

- Block elements: take the full width (new line). Examples: `<div>`, `<p>`, `<h1>`.
- Inline elements: flow within text. Examples: `<span>`, `<a>`, `<strong>`.
- Inline-block: inline flow but respects width/height.
- Helpful attributes:
  - `title` for native tooltip: `<button title="tip">Hover me</button>`
  - `href="tel:45444"` to open dialer: `<a href="tel:45444">Call</a>`
  - Data attributes: `data-product-name="(Shoes)"` → use in JS via `el.dataset.productName`

Semantic structure:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>My Page</title>
  </head>
  <body>
    <header>Header</header>
    <main>
      <section>
        <h1>Welcome</h1>
        <p>Intro content</p>
      </section>
    </main>
    <footer>Footer</footer>
  </body>
</html>
```

---

## 2) CSS Fundamentals

- Box model: `margin` (outside space), `border`, `padding` (inside space), `content`.
- Shorthands: `padding: 10px 4px;` → 10px vertical (T,B), 4px horizontal (L,R).
- Colors: `rgb(96,96,96)`, `rgba(0,0,0,0.5)`.
- Fonts: font stacks (backup) → `font-family: Roboto, Arial, sans-serif;`
- Placeholders: `.input::placeholder { color: #888; }`
- Box shadow (from notes): `box-shadow: inset 1px 2px 3px rgba(0,0,0,.15);`
- Always link assets first: CSS before closing `</head>`; JS before `</body>` for basic sites.

Quick snippet:

```html
<link rel="icon" type="image/x-icon" href="/favicon.ico" />
```

---

## 3) Layout: Flexbox and Grid

From your notes:

- Grid: row/column layout; `1fr` = fraction of free space
- Flexbox: one-dimensional (row or column). `flex: 1` roughly like `1fr` in some grid cases
- `justify-content` arranges items horizontally; `align-items` vertically (in rows)

Examples:

```css
.grid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr; /* 3 columns */
  gap: 16px;
}
.flex {
  display: flex;
  gap: 12px;
}
.flex > .item {
  flex: 1;
}
```

---

## 4) Positioning and the Box Model

From your notes:

- `position: fixed` sticks to the viewport; `absolute` positions inside the nearest positioned ancestor; `relative` keeps element in normal flow but sets a reference for absolute children.
- `top`, `left`, `right`, `bottom` offset from reference.
- Using left and right together can stretch an element.
- `z-index` controls stacking.
- `white-space: nowrap` prevents wrapping.
- `pointer-events: none` makes an element ignore mouse events.

Snippet:

```css
.floating-badge {
  position: fixed;
  top: 16px;
  right: 16px;
  z-index: 1000;
}
.card {
  position: relative; /* reference for absolute child */
}
.card .chip {
  position: absolute;
  top: 8px;
  right: 8px;
}
```

---

## 5) Icons, Media, and Tailwind Notes

- Special chars: `&#183;` (·), `&#8801;` (≡ hamburger)
- Video: `<video autoplay loop src="/media/promo.mp4"></video>`
- Tailwind quick notes from your file:
  - Run build: `npx tailwindcss -i ./src/input.css -o ./public/style.css --watch`
  - Mobile-first by default; use breakpoints like `md:text-4xl`
  - Custom values: `px-[2px]`, full viewport: `h-[100vh]`, max container: `max-w-[1170px]`, hide/scale: `scale-0 h-0`

---

## 6) JavaScript Basics

Core building blocks:

- Variables: `let`, `const`
- Types: number, string, boolean, null, undefined, object, symbol
- Functions: declarations and arrow functions
- Control flow: if/else, switch, loops (for, for-of, while)

Example (from `dom.htm` and your notes mindset):

```html
<button class="js-button">Second button</button>
<script>
  const btn = document.querySelector('.js-button')
  btn.addEventListener('click', () => alert('Clicked!'))
</script>
```

---

## 7) DOM Manipulation

Select, read, and update elements.

From `dom.htm`:

```html
<button>hello</button>
<button class="js-button">Second button</button>
<script>
  document.querySelector('button').innerHTML = 'Changed'
  const buttonElement = document.querySelector('.js-button')
  console.log(buttonElement)
</script>
```

Render dynamic text:

```js
document.querySelector('.result').innerHTML = '2'
```

---

## 8) Events and Interactions

You demonstrate many mouse/keyboard events in `events.html` and `events.js`.

Mouse events that toggle colors:

```js
const el = document.querySelector('.click')
el.addEventListener('click', () => el.classList.toggle('red'))
```

Keyboard example:

```js
const input = document.querySelector('input')
input.addEventListener('keypress', (e) => {
  console.log(e.key)
  console.log(e.shiftKey)
})
```

Mini counter (from `dom-prac.html`):

```js
let quantity = 0
function showCart() {
  document.querySelector('.para').innerHTML = `Cart quantity: ${quantity}`
}
function addToCart() {
  quantity++
  showCart()
}
function removeTwo() {
  quantity -= 2
  showCart()
}
```

---

## 9) Arrays and Iteration Methods

Map (from `map.js`):

```js
const numbers = [1, 2, 3, 4, 5]
const multiply = numbers.map((n) => n * 10)
// [10,20,30,40,50]
```

Filter (from `filter.js`):

```js
const ages = [32, 33, 16, 40]
const adults = ages.filter((age) => age >= 18)
// [32,33,40]
```

Reduce (from `reduce.js`):

```js
const nums = [1, 2, 3, 4, 5]
const sum = nums.reduce((prev, curr) => prev + curr, 0)
// 15
```

---

## 10) Objects, Maps, and Reduce Patterns

Objects are key-value stores; Maps allow non-string keys (from `map.js`):

```js
const map = new Map()
const keyObj = {}
map.set('a', 1)
map.set(keyObj, 'value')
console.log(map.size) // 2
for (const [k, v] of map) console.log(k, v)
```

Reduce for aggregations (from `reduce.js`):

```js
const peoples = [
  { name: 'A', age: 19 },
  { name: 'B', age: 29 },
  { name: 'C', age: 40 },
]
const oldest = peoples.reduce((p, c) => (c.age > p ? c.age : p), 0)
```

Word frequency pattern:

```js
const words = ['apple', 'banana', 'apple']
const freq = words.reduce((acc, w) => {
  acc[w] = (acc[w] || 0) + 1
  return acc
}, {})
```

---

## 11) Modules and Script Loading

From your notes:

- Modules keep variables scoped to a file; to export/import, mark the script as a module.

Example:

```html
<script type="module">
  import { add } from './utils.js'
  console.log(add(2, 3))
</script>
```

```js
// utils.js
export function add(a, b) {
  return a + b
}
```

`../` moves up a folder when importing files from nested directories.

---

## 12) Small DOM Mini-Projects

Calculator display and cart quantity (from `dom-prac.html`):

```js
function one() {
  document.querySelector('.result').innerHTML = '1'
}
function twoCalc() {
  document.querySelector('.result').innerHTML = '2'
}
```

Heads/Tails and name submit (commented in your file) give simple examples of conditionals and input handling:

```js
function heads() {
  document.querySelector('p').innerText = 'You chose: heads'
}
function tails() {
  document.querySelector('p').innerText = 'You chose: tails'
}
```

---

## 13) JavaScript OOP (Classes, Prototypes, Inheritance)

This section summarizes core OOP ideas from your `Web Dev/JS Basic/OOP` folder (classes, constructors, inheritance, encapsulation, polymorphism, prototypes, and prototypal inheritance).

### Classes and Constructors (from `classes.js`, `constructor.js`)

```js
class Person {
  constructor(name, age) {
    this.name = name
    this.age = age
  }
  greet() {
    return `Hi, I'm ${this.name} and I'm ${this.age}.`
  }
}

const p = new Person('Alex', 29)
console.log(p.greet())
```

Key points:

- `constructor` initializes instance fields.
- Methods defined in the class body live on `Person.prototype`.

### Inheritance and Method Overriding (from `inheritance.js`, `polymorphism.js`)

```js
class Animal {
  speak() {
    return 'generic sound'
  }
}

class Dog extends Animal {
  speak() {
    return 'woof'
  } // override
}

const a = new Animal()
const d = new Dog()
console.log(a.speak()) // generic sound
console.log(d.speak()) // woof
```

Polymorphism: different classes implement the same method interface but with different behavior.

### Encapsulation and Abstraction (from `encapsulation-abstraction.js`, `modifiers.js`)

Use closures or convention/fields to hide details and expose a simple API.

```js
function createCounter() {
  let value = 0 // private via closure
  return {
    inc() {
      value++
    },
    dec() {
      value--
    },
    get() {
      return value
    },
  }
}

const c = createCounter()
c.inc()
c.inc()
console.log(c.get()) // 2
```

In modern JS classes you can also use `#privateField` syntax:

```js
class BankAccount {
  #balance = 0
  deposit(amount) {
    if (amount > 0) this.#balance += amount
  }
  get balance() {
    return this.#balance
  }
}
```

### Prototypes and Prototypal Inheritance (from `prototype.js`, `prototypal-inheri.js`)

```js
function Car(make) {
  this.make = make
}
Car.prototype.honk = function () {
  return `${this.make} says beep`
}

const c1 = new Car('Toyota')
console.log(c1.honk()) // Toyota says beep
```

Linking objects directly with `Object.create`:

```js
const vehicle = {
  move() {
    return 'moving'
  },
}
const bike = Object.create(vehicle)
bike.ring = () => 'tring tring'
console.log(bike.move(), bike.ring())
```

### Async in OOP context (from `promises.js`, `async-await.js`)

```js
class Api {
  async getUser(id) {
    const res = await fetch(`/api/users/${id}`)
    if (!res.ok) throw new Error('Failed')
    return res.json()
  }
}
```

Takeaways:

- Prefer classes for modeling entities; inherit for shared behavior.
- For composition-heavy needs, favor object composition over deep inheritance.
- Encapsulate internal state (closures or `#private`) and expose minimal public methods.

---

## 14) More JavaScript Language Features

These topics are reflected across files in `Web Dev/JS Basic` (e.g., `destru-arr.js`, `spread-operator.js`, `rest-parameter.js`, `set.js`, `symbol.js`, `date.js`, `refer-prim.js`, iteration helpers):

- Destructuring:

  ```js
  const user = { name: 'Alex', age: 29 }
  const { name, age } = user
  const arr = [1, 2, 3]
  const [first, , third] = arr
  ```

- Spread and Rest:

  ```js
  const base = [1, 2]
  const combo = [...base, 3, 4]
  function sum(...nums) {
    return nums.reduce((a, b) => a + b, 0)
  }
  ```

- Set and Symbol:

  ```js
  const set = new Set([1, 1, 2]) // {1,2}
  const sym = Symbol('id')
  const obj = { [sym]: 123 }
  ```

- Date basics:

  ```js
  const now = new Date()
  now.getFullYear()
  ```

- Reference vs Primitive (from `refer-prim.js` insight): primitives copy by value; objects/arrays copy references.

---

## 15) Timers and Async (Callbacks, Promises, Async/Await)

Covered across `set-timeout.js`, `set-interval.js`, `promises.js`, `async-await.js`, and `async-callback.js`:

- Timers:

  ```js
  const id = setInterval(() => console.log('tick'), 1000)
  setTimeout(() => clearInterval(id), 5000)
  ```

- Promises and async/await:

  ```js
  const getData = () => new Promise((res) => setTimeout(() => res('ok'), 500))
  async function run() {
    const v = await getData()
    console.log(v)
  }
  run()
  ```

- Callback pattern:

  ```js
  function fetchUser(id, cb) {
    setTimeout(() => cb(null, { id }), 300)
  }
  fetchUser(1, (err, u) => {
    if (err) return console.error(err)
    console.log(u)
  })
  ```

---

## 16) Next Steps

- Explore your `JS Basic/OOP` folder deeper (e.g., `polymorphism.js`, `encapsulation-abstraction.js`).
- Build a small app combining OOP + DOM: e.g., a Todo with `TodoItem` class and list manager.
- Add unit tests (e.g., Vitest/Jest) for core functions and class methods.

With these notes and examples, you’ve got a clear foundation in HTML, CSS, and JS—grounded in your own code. Build on it by practicing small features and gradually composing full apps.
