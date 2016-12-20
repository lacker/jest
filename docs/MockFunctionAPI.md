---
id: mock-function-api
title: Mock Functions
layout: docs
category: API Reference
permalink: docs/mock-function-api.html
next: jest-object
---

#### Mock functions

Mock functions are also known as "spies", because they let you spy on the behavior of a function that is called indirectly by some other code, rather than just testing the output. You can create a mock function with `jest.fn()`.

  - [`mockFn.mock.calls`](#mockfnmockcalls)
  - [`mockFn.mock.instances`](#mockfnmockinstances)
  - [`mockFn.mockClear()`](#mockfnmockclear)
  - [`mockFn.mockImplementation(fn)`](#mockfnmockimplementationfn)
  - [`mockFn.mockImplementationOnce(fn)`](#mockfnmockimplementationoncefn)
  - [`mockFn.mockReset()`](#mockfnmockreset)
  - [`mockFn.mockReturnThis()`](#mockfnmockreturnthis)
  - [`mockFn.mockReturnValue(value)`](#mockfnmockreturnvaluevalue)
  - [`mockFn.mockReturnValueOnce(value)`](#mockfnmockreturnvalueoncevalue)

## Mock Functions

### `mockFn.mock.calls`
An array that represents all calls that have been made into this mock function. Each call is represented by an array of arguments that were passed during the call.

For example: A mock function `f` that has been called twice, with the arguments `f('arg1', 'arg2')`, and then with the arguments `f('arg3', 'arg4')` would have a `mock.calls` array that looks like this:

```js
[
  ['arg1', 'arg2'],
  ['arg3', 'arg4']
]
```

### `mockFn.mock.instances`
An array that contains all the object instances that have been instantiated from this mock function.

For example: A mock function that has been instantiated twice would have the following `mock.instances` array:

```js
var mockFn = jest.fn();

var a = new mockFn();
var b = new mockFn();

mockFn.mock.instances[0] === a; // true
mockFn.mock.instances[1] === b; // true
```

### `mockFn.mockClear()`
Resets all information stored in the [`mockFn.mock.calls`](#mockfn-mock-calls) and [`mockFn.mock.instances`](#mockfn-mock-instances) arrays.

Often this is useful when you want to clean up a mock's usage data between two assertions.

### `mockFn.mockReset()`
Resets all information stored in the mock

This is useful when you want to completely restore a mock back to its initial state.

### `mockFn.mockImplementation(fn)`
Accepts a function that should be used as the implementation of the mock. The mock itself will still record all calls that go into and instances that come from itself – the only difference is that the implementation will also be executed when the mock is called.

*Note: `jest.fn(implementation)` is a shorthand for `mockImplementation`.*

For example:

```js
const mockFn = jest.fn().mockImplementation(scalar => 42 + scalar);
// or: jest.fn(scalar => 42 + scalar);

const a = mockFn(0);
const b = mockFn(1);

a === 42; // true
b === 43; // true

mockFn.mock.calls[0][0] === 0; // true
mockFn.mock.calls[1][0] === 1; // true
```

`mockImplementation` can also be used to mock class constructors:

```
// SomeClass.js
module.exports = class SomeClass {
  m(a, b) {}
}

// OtherModule.test.js
const SomeClass = require('SomeClass')
const mMock = jest.fn()
SomeClass.mockImplementation(() => {
  return {
    m: mMock
  }
})

const some = new SomeClass()
some.m('a', 'b')
console.log('Calls to m: ', mMock.mock.calls)
```

### `mockFn.mockImplementationOnce(fn)`
Accepts a function that will be used as an implementation of the mock for one call to the mocked function. Can be chained so that multiple function calls produce different results.

```
var myMockFn = jest.fn()
  .mockImplementationOnce(cb => cb(null, true))
  .mockImplementationOnce(cb => cb(null, false));

myMockFn((err, val) => console.log(val));
> true

myMockFn((err, val) => console.log(val));
> false
```

When the mocked function runs out of implementations defined with mockImplementationOnce, it will execute the default implementation set with `jest.fn(() => defaultValue)` or `.mockImplementation(() => defaultValue)` if they were called:

```
var myMockFn = jest.fn(() => 'default')
  .mockImplementationOnce(() => 'first call')
  .mockImplementationOnce(() => 'second call');

console.log(myMockFn(), myMockFn(), myMockFn(), myMockFn());
> 'first call', 'second call', 'default', 'default'
```

### `mockFn.mockReturnThis()`
Just a simple sugar function for:

```js
jest.fn(function() {
  return this;
});
```

### `mockFn.mockReturnValue(value)`

Deprecated: Use `jest.fn(() => value)` instead.

```js
const mockNumberFn = jest.fn(() => 42);
mockNumberFn(); // 42

// Deprecated behavior:
jest.genMockFunction().mockImplementation(() => value);
```

### `mockFn.mockReturnValueOnce(value)`
Just a simple sugar function for:

```js
const valueReturned = false;
jest.fn(() => {
  if (!valueReturned) {
    valueReturned = true;
    return value;
  }
});
```