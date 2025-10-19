# The Complete Node.js Step‑by‑Step Learning Book

This guide walks you through Node.js fundamentals using the exact topics and example files in your `Web Dev/Node` folder. It’s practical, concise, and Windows‑friendly.

## Table of Contents

1. What is Node.js? How It Runs Code
2. The Module Wrapper and CommonJS Modules
3. ES Modules (import/export)
4. Core Modules Tour: path, fs, os, url
5. HTTP Servers and Responses
6. Manual Routing (without frameworks)
7. Serving HTML Pages from Disk
8. Events with EventEmitter
9. Streams: Large File IO
10. NPM Essentials and Scripts
11. Tips, Gotchas, and Next Steps

---

## 1) What is Node.js? How It Runs Code

Node.js is a JavaScript runtime built on V8, enabling JS on the server. Each file you run is wrapped in a function called the Module Wrapper that provides `exports`, `require`, `module`, `__filename`, and `__dirname` (in CommonJS).

---

## 2) The Module Wrapper and CommonJS Modules

CommonJS (CJS) uses `require()` and `module.exports`.

From `2. Modules/`:

```js
// 2. Modules/index.js
const greet = require("./greet");
const { person1, person2, person3 } = require("./peoples");

greet("HuXn");
console.log(person2);
console.log(person3);
greet(person1);
greet(person2);
```

```js
// 2. Modules/greet.js
function greet(username) {
	console.log("hello", username);
}
module.exports = greet;
```

```js
// 2. Modules/peoples.js
let person1 = "HuXn";
let person2 = "Modi";
let person3 = "John";
module.exports = { person1, person2, person3 };
```

Key points:
- Export one thing: `module.exports = fn`
- Export many: `module.exports = { a, b }`

---

## 3) ES Modules (import/export)

ESM uses `import`/`export`. Enable by adding `"type": "module"` to `package.json` or using `.mjs`.

From `3. ES6 Imports/`:

```js
// 3. ES6 Imports/index.js
import greet from "./greet.js";
import { p1, p2, p3 } from "./peoples.js";

greet("HuXn");
greet("Apple");
console.log(p1, p2, p3);
```

```js
// 3. ES6 Imports/greet.js
function greet(username) {
	console.log("Hello", username);
}
export default greet;
```

```js
// 3. ES6 Imports/peoples.js
let p1 = "huxn";
let p2 = "alex";
let p3 = "modi";
export { p1, p2, p3 };
```

Common patterns:
- Default export: `export default function …`
- Named exports: `export const X = …` and `import { X } from '…'`

---

## 4) Core Modules Tour: path, fs, os, url

### path

From `4. Path Module/index.js` (examples are commented in file):

```js
import path from "path";

// path.basename("c:\\node\\app.js")
// path.dirname("c:\\node\\app.js")
// path.extname("c:\\node\\app.js")
// path.join("c:", "huxn-webdev", "courses", "redux-toolkit")
// path.normalize("c:\\courses\\redux\\store\\features\\")
// path.parse("c:\\node\\app.js").base
```

### fs (Promise, Callback, Sync)

Promise API from `5. FS Module/1. Promise api/index.js`:

```js
import * as fs from "fs/promises";

// Create & write
await fs.writeFile("README.md", "Hello Nodejs");
await fs.appendFile("README.md", "Nodejs is best.");

// Copy & stat
await fs.copyFile("README.md", "info.txt");
const info = await fs.stat("info.txt");
console.log(info.isDirectory()); // false
console.log(info.isFile());      // true
```

Callback API from `5. FS Module/2. Callback api/index.js`:

```js
import * as fs from "fs";

fs.mkdir("d:\\nodesjs", function (error) {
	if (error) throw error;
	console.log("Directory Created ... ");
});
```

Sync API from `5. FS Module/3. Sync api/index.js`:

```js
import * as fs from "fs";
fs.mkdirSync("d:\\nodejs\\test\\demo", { recursive: true });
fs.rmdirSync("d:\\nodejs\\test\\demo");
```

When to use which:
- Promise: modern, async/await, best default
- Callback: legacy interop
- Sync: scripts/tools; avoid in servers

### os

From `6. OS Module/index.js`:

```js
import os from "os";
console.log(os.platform());
console.log(os.arch());
console.log(os.cpus());
console.log(os.hostname());
console.log(os.networkInterfaces());
console.log(os.freemem());
console.log(os.totalmem());
```

### url

From `7. URL/index.js`:

```js
import { URL } from "url";
const myURL = new URL("https://www.example.com:8080/p/a/t/h?query=string#hash");
console.log(myURL.hash);
console.log(myURL.host);
console.log(myURL.port);
console.log(myURL.protocol);
console.log(myURL.pathname);
console.log(myURL.href);
console.log(myURL.search);
console.log(myURL.searchParams);
console.log(myURL.toString());
```

---

## 5) HTTP Servers and Responses

From `8. HTTP Module/index.js`:

```js
import http from "http";

const server = http.createServer((req, res) => {
	// Set status and headers in one call
	res.writeHead(202, "Good", { "Content-Type": "text/html" });
	res.write("<h1>Hello From Node.js Server </h1>");
});

server.listen(8000, () => console.log("Server Up!"));
```

Notes:
- `res.writeHead(statusCode, statusMessage, headers)`
- call `res.end()` when you’re done writing (the example writes once; browsers will close the connection)

---

## 6) Manual Routing (without frameworks)

From `9. Routing/index.js`:

```js
import http from "http";

const server = http.createServer((req, res) => {
	if (req.url === "/") {
		res.writeHead(200, "OK", { "content-type": "text/html" });
		res.end("<a href='/contact'>Contact Page</a>");
	} else if (req.url === "/about") {
		res.writeHead(200, "OK", { "content-type": "text/html" });
		res.end("<h2>About Section </h2>");
	} else if (req.url === "/contact") {
		res.writeHead(200, "OK", { "content-type": "text/html" });
		res.end("<h3>Contact </h3>");
	} else {
		res.writeHead(400, "BAD", { "content-type": "text/html" });
		res.end(`<h1>404 page not found :( </h1>`);
	}
});

server.listen(8000, () => console.log("Server Up!..."));
```

Tip: Move route logic into functions or a simple router map for readability.

---

## 7) Serving HTML Pages from Disk

From `10. Serving Pages/index.js`:

```js
import http from "http";
import fs from "fs";

const server = http.createServer((req, res) => {
	if (req.url === "/") {
		res.writeHead(200, "OK", { "content-type": "text/html" });
		fs.readFile("../public/home.html", (error, data) => {
			if (error) throw error;
			res.end(data);
		});
	} else if (req.url === "/about") {
		res.writeHead(200, "OK", { "content-type": "text/html" });
		fs.readFile("../public/about.html", (error, data) => {
			if (error) throw error;
			res.end(data);
		});
	} else {
		res.writeHead(404, "BAD", { "content-type": "text/html" });
		res.end("<h3>Page Not Found :( </h3>");
	}
});

server.listen(8000, () => console.log("Server Up!"));
```

Tip: Prefer absolute paths (e.g., with `path.join(process.cwd(), 'public/home.html')`) to avoid relative path issues.

---

## 8) Events with EventEmitter

From `13. Events/index.js`:

```js
import EventEmitter from "events";
const events = new EventEmitter();

// Listen
events.on("response", (name, id) => {
	console.log(`user: ${name} id: ${id}`);
});

// Emit
events.emit("response", "huxn", 18);
events.emit("response", "john", 20);
```

Also available: `once` to listen a single time.

---

## 9) Streams: Large File IO

From `14. Streams/data.js` and `index.js`:

```js
// data.js – generate a big file
import fs from "fs";
for (let i = 0; i < 10000; i++) {
	fs.writeFileSync("./data.txt", `${i}\n`, { flag: "a" });
}
```

```js
// index.js – read it as a stream
import { createReadStream } from "fs";
const stream = createReadStream("./data.txt", { encoding: "utf8" });
stream.on("data", (chunk) => {
	console.log(chunk);
});
```

Why streams? Lower memory usage and backpressure handling for large files.

---

## 10) NPM Essentials and Scripts

Your modules include minimal `package.json` examples; the key bits:
- `name`, `version`, `main`/`type`, and `scripts`
- For ES Modules, set `"type": "module"`

Common scripts in PowerShell:

```powershell
npm init -y
npm install <pkg>
npm run dev
```

Tip: Use `nodemon` for auto-reload in dev: `npx nodemon 8.\ HTTP Module\index.js`

---

## 11) Tips, Gotchas, and Next Steps

- Use async/await with `fs/promises` for clean IO.
- Avoid sync fs calls in production server paths.
- Prefer absolute paths via `path.join(process.cwd(), ...)` when serving files.
- For real apps, use Express (see your Express guide) to simplify routing and middleware.
- Explore more core modules: `crypto`, `timers`, `cluster`.
- Next: connect Node to a database (e.g., MongoDB via Mongoose), add environment variables (`dotenv`), and build REST APIs.

You now have a practical Node.js reference grounded in your own code examples. Tweak the snippets and run them directly to reinforce each concept.
