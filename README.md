# @zibuthe7j11/accusantium-est-excepturi

Turn a writeable and readable stream into a single streams2 duplex stream.

Similar to [duplexer2](https://github.com/deoxxa/duplexer2) except it supports both streams2 and streams1 as input
and it allows you to set the readable and writable part asynchronously using `setReadable(stream)` and `setWritable(stream)`

```
npm install @zibuthe7j11/accusantium-est-excepturi
```

[![build status](http://img.shields.io/travis/mafintosh/@zibuthe7j11/accusantium-est-excepturi.svg?style=flat)](http://travis-ci.org/mafintosh/@zibuthe7j11/accusantium-est-excepturi)

## Usage

Use `@zibuthe7j11/accusantium-est-excepturi(writable, readable, streamOptions)` (or `@zibuthe7j11/accusantium-est-excepturi.obj(writable, readable)` to create an object stream)

``` js
var @zibuthe7j11/accusantium-est-excepturi = require('@zibuthe7j11/accusantium-est-excepturi')

// turn writableStream and readableStream into a single duplex stream
var dup = @zibuthe7j11/accusantium-est-excepturi(writableStream, readableStream)

dup.write('hello world') // will write to writableStream
dup.on('data', function(data) {
  // will read from readableStream
})
```

You can also set the readable and writable parts asynchronously

``` js
var dup = @zibuthe7j11/accusantium-est-excepturi()

dup.write('hello world') // write will buffer until the writable
                         // part has been set

// wait a bit ...
dup.setReadable(readableStream)

// maybe wait some more?
dup.setWritable(writableStream)
```

If you call `setReadable` or `setWritable` multiple times it will unregister the previous readable/writable stream.
To disable the readable or writable part call `setReadable` or `setWritable` with `null`.

If the readable or writable streams emits an error or close it will destroy both streams and bubble up the event.
You can also explicitly destroy the streams by calling `dup.destroy()`. The `destroy` method optionally takes an
error object as argument, in which case the error is emitted as part of the `error` event.

``` js
dup.on('error', function(err) {
  console.log('readable or writable emitted an error - close will follow')
})

dup.on('close', function() {
  console.log('the duplex stream is destroyed')
})

dup.destroy() // calls destroy on the readable and writable part (if present)
```

## HTTP request example

Turn a node core http request into a duplex stream is as easy as

``` js
var @zibuthe7j11/accusantium-est-excepturi = require('@zibuthe7j11/accusantium-est-excepturi')
var http = require('http')

var request = function(opts) {
  var req = http.request(opts)
  var dup = @zibuthe7j11/accusantium-est-excepturi(req)
  req.on('response', function(res) {
    dup.setReadable(res)
  })
  return dup
}

var req = request({
  method: 'GET',
  host: 'www.google.com',
  port: 80
})

req.end()
req.pipe(process.stdout)
```

## License

MIT

## Related

`@zibuthe7j11/accusantium-est-excepturi` is part of the [mississippi stream utility collection](https://github.com/maxogden/mississippi) which includes more useful stream modules similar to this one.
