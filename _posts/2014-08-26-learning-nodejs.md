---
layout: post
title: "nodejs入门"
description: "nodejs入门"
category: nodejs
tags: [nodejs, js ]
---
{% include JB/setup %}

```js
console.log("hello world");
```

`global process object` has `argv` containing the command line

```js
console.log(process.argv);
```

`Number()` transform String to number`

#### sum
```
for (var i = 2; i < process.argv.length; i++)
      result += Number(process.argv[i])
```
### Sync IO system

``` js 
//loading module
var fs = require('fs')
```

```
fs.readFileSync('/path/to/file')
//return a Buffer object containing the complete contents of the file.
```

Buffer buffer.toString()

String string.split()

```js
var fs = require('fs')
var contents = fs.readFileSync(process.argv[2])
var lines = contents.toString().split('\n').length - 1
console.log(lines)
    
// note you can avoid the .toString() by passing 'utf8' as the
// second argument to readFileSync, then you'll get a String!
//
// fs.readFileSync(process.argv[2], 'utf8').split('\n').length - 1
```

### Async IO system

```
fs.readFile()
```

``` 
//callback
function callback (err, data) { /* ... */ }
```

```
var fs = require('fs')
var file = process.argv[2]
fs.readFile(file, function (err, contents) {
// fs.readFile(file, 'utf8', callback) can also be used
var lines = contents.toString().split('\n').length - 1
console.log(lines)
})
```

`fs.readdir()` 
`function callback (err, list) { /* ... */ }`

```js
var fs = require('fs')
var path = require('path')

fs.readdir(process.argv[2], function (err, list) {
  list.forEach(function (file) {
    if (path.extname(file) === '.' + process.argv[3])
      console.log(file)
  })
})
```

### module

``` js
module.exports = function (args) { /* ... */ }
```

```js
var mymodule = require('./mymodule.js')
```

```js
var filterFn = require('./solution_filter.js')
var dir = process.argv[2]
var filterStr = process.argv[3]

filterFn(dir, filterStr, function (err, list) {
  if (err)
    return console.error('There was an error:', err)

  list.forEach(function (file) {
    console.log(file)
  })
})
```

```js
var fs = require('fs')
var path = require('path')

module.exports = function (dir, filterStr, callback) {

  fs.readdir(dir, function (err, list) {
    if (err)
      return callback(err)

    list = list.filter(function (file) {
      return path.extname(file) === '.' + filterStr
    })

    callback(null, list)
  })
}
```

### http client

```
http
```

`http.get()`

```
function callback (response) { /* ... */ }
```

`response` is a **Node Stream object**

```
var http = require('http')

http.get(process.argv[2], function (response) {
  response.setEncoding('utf8')
  response.on('data', console.log)
  response.on('error', console.error)
})
```

### http collect

```
response.pipe(bl(function (err, data) { /* ... */ }))
// or
response.pipe(concatStream(function (data) { /* ... */ }))
```

``` js
var http = require('http')
var bl = require('bl')

http.get(process.argv[2], function (response) {
  response.pipe(bl(function (err, data) {
    if (err)
      return console.error(err)
    data = data.toString()
    console.log(data.length)
    console.log(data)
  }))  
})
```

### juggling async

**Counting callbacks**

```js
var http = require("http");
var concatStream = require("concat-stream");
var i = 0;
var count = 0;
var list = [];
for(i = 0; i < 3; ++i) {
   var n = i;
   http.get(process.argv[n + 2], function(response){
       response.pipe(concatStream(function(data){
          console.log("n " + n);                                          
          list[n] = data.toString();
          count++;
          console.log("count " + count);
          if(count == 3) {
              console.log(list[1])
          }
      }));
  });
}
\\this is not right, every thread's n is 2;
```

```
var http = require('http')
var bl = require('bl')
var results = []
var count = 0

function printResults () {
  for (var i = 0; i < 3; i++)
    console.log(results[i])
}

function httpGet (index) {
  http.get(process.argv[2 + index], function (response) {
    response.pipe(bl(function (err, data) {
      if (err)
        return console.error(err)

      results[index] = data.toString()
      count++

      if (count == 3) // yay! we are the last one!
        printResults()
    }))
  })
}

for (var i = 0; i < 3; i++)
   httpGet(i)
```

### time server

`net module`

`Server server = net.createServer(callback)`
`function callback (socket) { /* ... */ }`
`server.listen(port)`
`socket.write(data)`
`socket.end()`  or `socket.end(data)`
`new Date()`

```js
var net = require("net");
var strftime = require("strftime");

var server = net.createServer(function(socket){
    socket.end(strftime("%Y-%m-%d %H:%M\n", new Date()));                   
});
server.listen(process.argv[2]);
```

`http://strftime.net/` time format

### http file server

`http.createServer(callback)`
`function callback (request, response) { /* ... */ }`
`Both request and response are also Node streams`
`fs.createReadStream()`
`src.pipe(dst)`

```
var http = require('http')
var fs = require('fs')

var server = http.createServer(function (req, res) {
  res.writeHead(200, { 'content-type': 'text/plain' })

  fs.createReadStream(process.argv[3]).pipe(res)
})

server.listen(Number(process.argv[2]))
```

### http uppercaserer

```
var http = require('http')
var map = require('through2-map')

var server = http.createServer(function (req, res) {
  if (req.method != 'POST')
    return res.end('send me a POST\n')

  req.pipe(map(function (chunk) {
    return chunk.toString().toUpperCase()
  })).pipe(res)
})

server.listen(Number(process.argv[2]))
```

### http json api server

`'url' module`
`url.parse(request.url, true)`

`node -pe "require('url').parse('/test?q=1', true)"`

```
{ protocol: null,
  slashes: null,
  auth: null,
  host: null,
  port: null,
  hostname: null,
  hash: null,
  search: '?q=1',
  query: { q: '1' },
  pathname: '/test',
  path: '/test?q=1',
  href: '/test?q=1' }
```
  
```
JSON.stringify() 
```

```
res.writeHead(200, { 'Content-Type': 'application/json' })
```

```
var http = require('http')
var url = require('url')

function parsetime (time) {
  return {
    hour: time.getHours(),
    minute: time.getMinutes(),
    second: time.getSeconds()
  }
}

function unixtime (time) {
  return { unixtime : time.getTime() }
}

var server = http.createServer(function (req, res) {
  var parsedUrl = url.parse(req.url, true)
  var time = new Date(parsedUrl.query.iso)
  var result

  if (/^\/api\/parsetime/.test(req.url)) //regular expressions
    result = parsetime(time)
  else if (/^\/api\/unixtime/.test(req.url))
    result = unixtime(time)

  if (result) {
    res.writeHead(200, { 'Content-Type': 'application/json' })
    res.end(JSON.stringify(result))
  } else {
    res.writeHead(404)
    res.end()
  }
})
server.listen(Number(process.argv[2]))
```


