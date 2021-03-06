## Node.js

#### HTTP Module

We can use HTTP module to build a simple backend.

```js
const http = require('http')

const server = http.createServer((req, res) => {
  // handle the root route
  if (req.url === '/') {
    res.write('hello world')
    res.end()
  }

  // the api route returns a string which represents a JSON object
  if (req.url === '/api/courses') {
    res.write(JSON.stringify({
      id: 0,
      name: 'dennis'
    }))
    res.end()
  }
})

server.listen(3000)

console.log('listening on port 3000')
```

#### Path Module

The `path` module makes your life easier when working with path.

```js
const path = require('path');

/*
 * parse the string variable __filename into an object
    { 
      root: '/',
      dir: '/Users/dennisxiao/Practices/nodejs',
      base: 'test.js',
      ext: '.js',
      name: 'test' 
    }
*/
const pathObj = path.parse(__filename);
```

#### File System Module

The `fs` module contains both synchronous and asynchronous methods to deal with files, however, we should always use the asyn ones in our app. The asyn methods usually take a callback function, which will be executed when the file operation is done.

```js
const fs = require('fs');

fs.readdir('./', (err, files) => {
  if (err) {
    // handle errors
  } else {
    console.log(files);
  }
})
```

#### Event Module

```js
// EventEmitter is a class
const EventEmitter = require('events');
// create an instance of EventEmitter
const emitter = new EventEmitter();

// register a listener, you can use both addListener() or on() to register a listener
// when a 'messageLogged' event happens, the callback function will be executed
emitter.on('messageLogged', (arg) => {
  // the arg is the data emitted from the event messageLogged event
  console.log(arg)
})

// emit an event with an object
emitter.emit('messageLogged', {
  id: 1,
  url: 'http://dennis.com'
})
```

It is important to remember you need to register the event before emitting them.

However, it is not usual to work directly with an EventEmitter instance. We normally create a class and extend EventEmitter.

```js
/* logger.js */

const EventEmitter = require('events')

// extend EventEmitter class so the Logger class can also emit an event
class Logger extends EventEmitter {
  
  // define a method
  log (message) {
    console.log(message)

    // emit a messageLogged event with an object
    this.emit('messageLogged', {
      id: 1,
      url: 'http://dennis.com'
    })
  }
}

// export the Logger class
module.exports = Logger;
```

```js
/* app.js */

// import the Logger class
const Logger = require('./logger')
// create a Logger instance
const logger = new Logger()

// register a listener on messageLogged event
// which can be emitted by the log(message) method in the Logger class
// when a messageLogged event happens, a callback function is executed
logger.on('messageLogged', (arg) => {
  console.log(arg)
})

// call the log() method
logger.log('message')
```

#### Fundamentals

Node.js is not a programming language or framework. It is a runtime environment written in C++ for executing Javascript code with the V8 Javascript engine embedded. It is suitable for real time application but not CPU-intensive application.

When we declare a variable in a client-side Javascript environment (browser), it is automatically added to the `window` global object. However, in Node environment, variables are scoped to the file in which they are declared and would not contaminate the `global` object. This is because in Node, every file lives in a separate module, which means variables and functions declared inside can only be accessed within the file. Node achieves this modularity by wrapping your code in an IIFE `function (exports, require, module, __filename, __dirname)` in each file. The `require` method, `exports` and `module` objects you can access in each file are actually being passed into the IIFE as parameters.

Now take a look at the parameters in the IIFE. Think of `module.exports` as the variable that gets returned from `require()`. It is an empty object by default, and it is fine to change to anything. The parameter `exports` itself is never returned. It is just a reference to `module.exports`; a convenience variable to help module authors write less code. Working with its properties is safe and recommended.

Node uses 'CommonJS' for its module loader. If you want to expose any variable or function to other modules. You need to explicitly export them in the module. 

```js
const url = 'http://dennisxiao.com';

function log(message) {
  // send an HTTP request to the URL to log messages to the cloud
}

// export a variable called endPoint and the log method
// now if you console.log(module), you would see them in the exports object
// exports: { endPoint: 'http://dennisxiao.com', log: [Function: log] }
module.exports.endPoint = url;
module.exports.log = log;

// you can also export in this way
module.exports = {
  endPoint: url,
  log: log
}

// check the info of the current module
console.log(module);
```

In order to load a module, you need to use `require()` to import the module.

```js
// the variable logger now stores whatever is being exported in logger module
const logger = require(./logger);
```

It is noted that in a Node module, the keyword `this` refers to `module.exports`.

#### Set up a NODE_PATH

When you require modules in a complex project, it would get messy soon when you have to deal with file paths. You can set up a `NODE_PATH` in your package.json file to specify your module path.

```js
"scripts": {
  // set NODE_PATH to the lib folder, so all the files use this 
  "start": "NODE_PATH=./lib node index.js"
}
```

#### Node REPL

Get into Node REPL shell (Read–Eval–Print Loop) by typing `node`.

In Node REPL, the `this` keyword is equal to the `global` object, however, in any Node module, the top level `this` refers to `module.exports`.

In Node REPL, the `_` is set to be the result of the last operation.

```
1 + 1 ==> 2

_ + 1 ==> 3
```

- `.save fileName.js` and `.load fileName.js` to save / load a file
- `.editor` to get into editor mode
- `.help` to get help
- `.exit` to exit Node REPL
