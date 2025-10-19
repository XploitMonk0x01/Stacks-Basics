# The Complete Express.js Learning Guide

This guide teaches Express.js from the ground up using real examples from your `Web Dev/Express-js` project. It’s concise, practical, and aligned to your codebase (routers, controllers, EJS views, static files, JSON APIs, middleware, params and queries).

## Table of Contents

1. Introduction and Setup
2. Hello World and Basic Routing
3. Route Patterns and Multiple Handlers
4. Query Strings and Route Params
5. JSON APIs and Data Responses
6. Static Files (HTML/CSS/JS)
7. Views with EJS Templates
8. Routers and Controllers (Project Structure)
9. Middleware (Global and Per-Route)
10. Error Handling and 404s
11. CORS, Env Vars, and Useful Middleware
12. Security and Best Practices
13. Run and Troubleshoot on Windows

---

## 1) Introduction and Setup

- Express.js is a minimal web framework for Node.js.
- Your project uses ES Modules (import/export) via `"type": "module"` in `package.json`.

Key files in your project:

- `index.js` – basic routes and advanced patterns
- `ejs.js` – EJS view engine + mounted router
- `routes/` + `controllers/` – organized routes and controller handlers
- `serving-files.js` + `public/` – static files (index.html, style.css, app.js)
- `json.js` + `products.js` – JSON API example
- `middleware-func.js` + `middlewares/logs.js` – custom middleware
- `route-params.js` and `query-string.js` – params & query demos

Dependencies (from `package.json`):

- express, ejs, nodemon (dev server)

---

## 2) Hello World and Basic Routing

A simple Express server that returns HTML strings on different routes. From `index.js`:

```js
import express from 'express'
const app = express()

app.get('/', (req, res) => {
  res.send('<h1>Welcome to HOME</h1>')
})

app.get('/about', (req, res) => {
  res.send('<h1> ABOUT 🧖‍♂️</h1>')
})

app.get('/contact', (req, res) => {
  res.send('<h1> Contact</h1>')
})

app.listen(8000, () => console.log('Server Up!'))
```

Notes:

- `app.get(path, handler)` registers a GET route.
- `res.send()` can return strings, HTML, JSON, Buffers, etc.

---

## 3) Route Patterns and Multiple Handlers

Express supports string patterns and regex paths. Also, you can attach multiple handlers per route and use `next()` to pass control.

From `index.js`:

```js
// String pattern: '/ab?cd' matches '/acd' or '/abcd'
app.get('/ab?cd', (req, res) => {
  res.send('If the user hits (acd) or (abcd) then this will run.')
})

// Regex: matches any path containing the letter 'x'
app.get(/x/, (req, res) => {
  res.send('If the path includes the letter (x) it will work.')
})

// Exact regex: '/users/1234' (4 digits after /users/)
app.get(/^\/users\/[0-9]{4}$/, (req, res) => {
  res.send('Working')
})
```

Multiple callbacks and `next()`:

```js
app.get(
  '/double-cb',
  (req, res, next) => {
    console.log('First Callback')
    next()
  },
  (req, res) => {
    res.send('Second Callback')
  }
)

const cb1 = (req, res, next) => {
  console.log('First Callback')
  next()
}
const cb2 = (req, res, next) => {
  console.log('Second Callback')
  next()
}
const cb3 = (req, res) => {
  console.log('Third Callback')
  res.send('Array of Callbacks')
}

app.get('/array-cb', [cb1, cb2, cb3])
```

Tip: You can also use `app.route("/path").get(...).post(...).put(...).delete(...)` to group handlers (see commented code in `index.js`).

---

## 4) Query Strings and Route Params

Route params live in the path, query strings in the URL after `?`.

Route params example from `route-params.js`:

```js
app.get('/product/order/:day/:month/:year', (req, res) => {
  const { day, month, year } = req.params
  res.send(`Product was ordered on: ${day}/${month}/${year}`)
})
```

Query strings example from `query-string.js`:

```js
app.get('/product', (req, res) => {
  const { category, id } = req.query // e.g. /product?category=sneakers&id=42
  res.send(`Product Category: ${category} & Product ID: ${id}`)
})
```

---

## 5) JSON APIs and Data Responses

Use `res.json()` to return JSON. From `json.js`:

```js
import express from 'express'
import products from './products.js'
const app = express()

app.get('/products', (req, res) => {
  res.json(products)
})

app.listen(8000, () => console.log('Server Up!..'))
```

`products.js` exports an array of product objects—perfect for a demo API.

---

## 6) Static Files (HTML/CSS/JS)

Serve a `public/` folder and an `index.html` with CSS and JS. From `serving-files.js`:

```js
import express from 'express'
import path from 'path'
const app = express()

// 1) Serve everything in ./public at the site root
app.use(express.static('./public'))

// 2) Or explicitly send a file
app.get('/', (req, res) => {
  res.sendFile(path.join(process.cwd(), './public/index.html'))
})

app.listen(8000, () => console.log('Server Up!..'))
```

Your `public/` folder contains `index.html`, `style.css`, and `app.js` with a simple button click handler.

---

## 7) Views with EJS Templates

Use the EJS view engine for server-rendered pages. From `ejs.js`:

```js
import express from 'express'
import router from './routes/route.js'
const app = express()

app.set('view engine', 'ejs') // tell Express we use EJS
app.use('/', router) // mount routes that render views

app.listen(8000, () => console.log('Server Up!..'))
```

Your controllers render templates:

```js
// controllers/homeController.js
const homeController = (req, res) => {
  const data = { name: 'Pandu', userId: 2 }
  res.render('index', data)
}

// controllers/aboutController.js
const aboutController = (req, res) => {
  res.render('about')
}
```

Your views live in `views/`. In `views/index.ejs` you demonstrate variables, conditionals, and loops.

Common EJS patterns:

```ejs
<h1>Welcome <%= name %></h1>
<% if (userId === 2) { %>
	<p>Special user</p>
<% } %>

<% for (const item of [1,2,3]) { %>
	<div><%= item %></div>
<% } %>
```

---

## 8) Routers and Controllers (Project Structure)

Split routes into modular routers and keep business logic in controllers.

Router that maps paths to controllers (`routes/route.js`):

```js
import express from 'express'
import { homeController } from '../controllers/homeController.js'
import { aboutController } from '../controllers/aboutController.js'
const router = express.Router()

router.get('/', homeController)
router.get('/about', aboutController)

export default router
```

Feature router using CRUD-like endpoints (`routes/user.js` + `controllers/student.js`):

```js
// routes/user.js
import express from 'express'
import {
  allStudents,
  newStudent,
  updateStudent,
  deleteStudent,
} from '../controllers/student.js'
const router = express.Router()

router.get('/all', allStudents)
router.post('/create', newStudent)
router.put('/update', updateStudent)
router.delete('/delete', deleteStudent)

export default router
```

```js
// controllers/student.js
const allStudents = (req, res) => res.send('All Users')
const newStudent = (req, res) => res.send('Create new User')
const updateStudent = (req, res) => res.send('Update User')
const deleteStudent = (req, res) => res.send('Delete User')

export { allStudents, newStudent, updateStudent, deleteStudent }
```

Mount the router in an app (example from `controllers.js`):

```js
import express from 'express'
import student from './routes/user.js'
const app = express()

app.use('/student', student)
app.listen(8000, () => console.log('Server Up!..'))
```

---

## 9) Middleware (Global and Per-Route)

Middleware runs between the incoming request and your route handler. Use it to log, auth, parse, etc.

Custom logger middleware (`middlewares/logs.js`):

```js
function userCredentials(req, res, next) {
  console.log('username: (alex)')
  console.log('email: (alex21@gmail.com)')
  console.log('password: (alex22332)')
  console.log('age: (18)')
  next()
}
export default userCredentials
```

Apply globally and per route (`middleware-func.js`):

```js
import express from 'express'
import userCredentials from './middlewares/logs.js'
const app = express()

app.use(userCredentials) // global

app.get('/', userCredentials, (req, res) => {
  res.send('<h1>Hello Admin</h1>')
})
```

Built-in middleware to remember:

- `express.json()` – parse JSON bodies
- `express.urlencoded({ extended: true })` – parse form posts
- `express.static("public")` – serve static files

---

## 10) Error Handling and 404s

Create a 404 route and a centralized error handler. Pattern:

```js
// 404 handler (after all routes)
app.use((req, res) => {
  res.status(404).send('Not Found')
})

// Error-handling middleware (signature with 4 args)
app.use((err, req, res, next) => {
  console.error(err.stack)
  res.status(500).json({ message: 'Internal Server Error' })
})
```

Attach `next(err)` in routes to delegate unexpected errors here.

---

## 11) CORS, Env Vars, and Useful Middleware

- CORS: Cross-Origin Resource Sharing controls which origins can access your API. If your frontend URL/port differs from your API, enable it.

  ```js
  // npm i cors
  import cors from 'cors'
  app.use(cors()) // allow all origins (dev)
  // or: app.use(cors({ origin: "http://localhost:5173" }));
  ```

- Environment variables with dotenv (from your `backend.txt` note):

  ```js
  // npm i dotenv
  import 'dotenv/config' // loads .env into process.env
  // OR: import dotenv from "dotenv"; dotenv.config();

  const PORT = process.env.PORT ?? 8000
  app.listen(PORT, () => console.log(`Server listening on ${PORT}`))
  ```

Other handy middleware:

- `morgan` (HTTP logging)
- `helmet` (secure headers)
- `compression` (gzip responses)

---

## 12) Security and Best Practices

- Validate/sanitize inputs (e.g., `zod`, `joi`, or custom checks)
- Never log raw passwords (your logger is for demo only)
- Use HTTPS and secure cookies in production
- Rate-limit sensitive endpoints
- For auth: hash passwords with `bcrypt` and sign tokens with `jsonwebtoken` (JWT)

Example (conceptual):

```js
// npm i bcrypt jsonwebtoken
import bcrypt from 'bcrypt'
import jwt from 'jsonwebtoken'

const hash = await bcrypt.hash(plaintextPassword, 10)
const ok = await bcrypt.compare(plaintextPassword, hash)

const token = jwt.sign({ userId }, process.env.JWT_SECRET, { expiresIn: '1h' })
```

---

## 13) Run and Troubleshoot on Windows

Your `package.json` includes a dev script that starts the server powering EJS routes:

```json
{
  "scripts": {
    "dev": "nodemon ejs.js"
  }
}
```

Typical workflow (PowerShell):

```powershell
npm install
npm run dev
```

Tips:

- If you run a different entry (e.g., `index.js`, `json.js`, or `serving-files.js`), start nodemon manually: `npx nodemon index.js`
- If port 8000 is busy, change it or set `PORT` in a `.env` and read `process.env.PORT`.
- With ES Modules, ensure `"type": "module"` in `package.json` and use `import` syntax.

---

### Quick Reference

- Basic Route: `app.get("/", (req, res) => res.send("Hello"))`
- JSON Response: `res.json({ ok: true })`
- Static: `app.use(express.static("public"))`
- View Engine: `app.set("view engine", "ejs")`; `res.render("index", data)`
- Router: `const router = express.Router(); app.use("/api", router)`
- Params: `req.params.id`; Query: `req.query.q`
- Middleware: `app.use((req,res,next)=>{ /*...*/ next(); })`
- Error Handler: `(err, req, res, next) => { /*...*/ }`

You now have a complete, example-backed Express.js playbook that mirrors your project. Build from here by adding database access (e.g., Mongoose), authentication, and robust validations.
