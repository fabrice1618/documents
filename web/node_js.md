# node.js

- node.js
- Node.js® is a JavaScript runtime built on Chrome’s V8 JavaScript engine.

## hello world
- create helloworld.js
```javascript
    const http = require('http');
    const hostname = '127.0.0.1';
    const port = 3000;
    const server = http.createServer((req, res) => {
        res.statusCode = 200;
        res.setHeader('Content-Type', 'text/plain');
        res.end('Hello World\n');
        }
    );
    server.listen(port, hostname, () => {
        console.log(`Server running at http://${hostname}:${port}/`);
        }
    );
```