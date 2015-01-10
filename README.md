# node-promise-retry [![Build Status](https://travis-ci.org/IndigoUnited/node-promise-retry.svg?branch=master)](https://travis-ci.org/IndigoUnited/node-promise-retry)

Retries a function that returns a promise, leveraging the power of the [retry](https://github.com/tim-kos/node-retry) module.

There's already some modules that are able to retry functions that return promises but
they were rather difficult to use or do not offer an easy to do conditional retries.


## Installation

`$ npm install promise-retry`


## Usage

### promiseRetry(fn, [options])

Calls `fn` until the returned promise ends up fulfilled or rejected with an error different than
a `retry` error.   
The `options` argument is an object which maps to the [retry](https://github.com/tim-kos/node-retry) module options:

- `retries`: The maximum amount of times to retry the operation. Default is `10`.
- `factor`: The exponential factor to use. Default is `2`.
- `minTimeout`: The number of milliseconds before starting the first retry. Default is `1000`.
- `maxTimeout`: The maximum number of milliseconds between two retries. Default is `Infinity`.
- `randomize`: Randomizes the timeouts by multiplying with a factor between `1` to `2`. Default is `false`.


The `fn` function will receive a `retry` function as its first argument that should be called with an error whenever you want to retry `fn`. The `retry` function will always throw an error.   
If there's retries left, it will throw a special `retry` error that will be handled internally to call `fn` again.
If there's no retries left, it will throw the actual error passed to it.


```js
var promiseRetry = require('promise-retry');

// Simple example
promiseRetry(function (retry) {
    return doSomething()
    .catch(retry);
})
.then(function (value) {
    // ..
}, function (err) {
    // ..
});

// Conditional example
promiseRetry(function (retry) {
    return doSomething()
    .catch(function (err) {
        if (err.code === 'ETIMEDOUT') {
            retry(err);
        }

        throw err;
    })
});
```


## Tests

`$ npm test`


## License

Released under the [MIT License](http://www.opensource.org/licenses/mit-license.php).
