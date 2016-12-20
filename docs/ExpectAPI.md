---
id: expect
title: Expect
layout: docs
category: API Reference
permalink: docs/expect.html
next: mock-function-api
---

#### Writing assertions with `expect`

When you're writing tests, you need to check that values are what you
expect all the time. That's what you use `expect` for.

  - [`expect(value)`](#expectvalue)
  - [`expect.extend(matchers)`](#extending-jest-matchers)
  - [`expect.<asymmetric-match>()`](#asymmetric-matchers)
  - `.lastCalledWith(arg1, arg2, ...)` is an alias for [`.toHaveBeenLastCalledWith(arg1, arg2, ...)`](#tohavebeenlastcalledwitharg1-arg2-)
  - [`.not`](#not)
  - [`.toBe(value)`](#tobevalue)
  - `.toBeCalled()` is an alias for [`.toHaveBeenCalled()`](#tohavebeencalled)
  - `.toBeCalledWith(arg1, arg2, ...)` is an alias for [`.toHaveBeenCalledWith(arg1, arg2, ...)`](#tohavebeencalledwitharg1-arg2-)
  - [`.toBeCloseTo(number, numDigits)`](#tobeclosetonumber-numdigits)
  - [`.toBeDefined()`](#tobedefined)
  - [`.toBeFalsy()`](#tobefalsy)
  - [`.toBeGreaterThan(number)`](#tobegreaterthannumber)
  - [`.toBeGreaterThanOrEqual(number)`](#tobegreaterthanorequalnumber)
  - [`.toBeLessThan(number)`](#tobelessthannumber)
  - [`.toBeLessThanOrEqual(number)`](#tobelessthanorequalnumber)
  - [`.toBeInstanceOf(Class)`](#tobeinstanceofclass)
  - [`.toBeNull()`](#tobenull)
  - [`.toBeTruthy()`](#tobetruthy)
  - [`.toBeUndefined()`](#tobeundefined)
  - [`.toContain(item)`](#tocontainitem)
  - [`.toContainEqual(item)`](#tocontainequalitem)
  - [`.toEqual(value)`](#toequalvalue)
  - [`.toHaveBeenCalled()`](#tohavebeencalled)
  - [`.toHaveBeenCalledTimes(number)`](#tohavebeencalledtimesnumber)
  - [`.toHaveBeenCalledWith(arg1, arg2, ...)`](#tohavebeencalledwitharg1-arg2-)
  - [`.toHaveBeenLastCalledWith(arg1, arg2, ...)`](#tohavebeenlastcalledwitharg1-arg2-)
  - [`.toHaveLength(number)`](#tohavelengthnumber)
  - [`.toMatch(regexp)`](#tomatchregexp)
  - [`.toMatchObject(object)`](#tomatchobjectobject)
  - [`.toMatchSnapshot()`](#tomatchsnapshot)
  - [`.toThrow()`](#tothrow)
  - [`.toThrowError(error)`](#tothrowerrorerror)
  - [`.toThrowErrorMatchingSnapshot()`](#tothrowerrormatchingsnapshot)

## Writing assertions with `expect`

### `expect(value)`

The `expect` function is used every time you want to test a value. You will rarely call `expect` by itself. Instead, you will use `expect` along with a "matcher" function to assert something about a value.

It's easier to understand this with an example. Let's say you have a method `bestLaCroixFlavor()` which is supposed to return the string `'grapefruit'`.
Here's how you would test that:

```js
describe('the best La Croix flavor', () => {
  it('is grapefruit', () => {
    expect(bestLaCroixFlavor()).toBe('grapefruit');
  });
});
```

In this case, `toBe` is the matcher function. There are a lot of different matcher functions, documented below, to help you test different things.

The argument to `expect` should be the value that your code produces, and any argument to the matcher should be the correct value. If you mix them up, your tests will still work, but the error messages on failing tests will look strange.

### `.not`

If you know how to test something, `.not` lets you test its opposite. For example, this code tests that the best La Croix flavor is not coconut:

```js
describe('the best La Croix flavor', () => {
  it('is not coconut', () => {
    expect(bestLaCroixFlavor()).not.toBe('coconut');
  });
});
```

### `.toBe(value)`

`toBe` just checks that a value is what you expect. It uses `===` to check
strict equality.

For example, this code will validate some properties of the `beverage` object:

```js
const can = {
  ounces: 12,
  name: 'pamplemousse',
};

describe('the can', () => {
  it('has 12 ounces', () => {
    expect(can.ounces).toBe(12);
  });

  it('has a sophisticated name', () => {
    expect(can.name).toBe('pamplemousse');
  });
});
```

Don't use `toBe` with floating-point numbers. For example, due to rounding, in JavaScript `0.2 + 0.1` is not strictly equal to `0.3`. If you have floating point numbers, try `.toBeCloseTo` instead.

### `.toHaveBeenCalled()`

Use `.toHaveBeenCalled` to ensure that a mock function got called.

For example, let's say you have a `drinkAll(drink, flavor)` function that takes a `drink` function and applies it to all available beverages. You might want to check that `drink` gets called for `'lemon'`, but not for `'octopus'`, because `'octopus'` flavor is really weird and why would anything be octopus-flavored? You can do that with this test suite:

```js
describe('drinkAll', () => {
  it('drinks something lemon-flavored', () => {
    let drink = jest.fn();
    drinkAll(drink, 'lemon');
    expect(drink).toHaveBeenCalled();
  });

  it('does not drink something octopus-flavored', () => {
    let drink = jest.fn();
    drinkAll(drink, 'octopus');
    expect(drink).not.toHaveBeenCalled();
  });
});
```

### `.toHaveBeenCalledTimes(number)`

Use `.toHaveBeenCalledTimes` to ensure that a mock function got called exact number of times.

For example, let's say you have a `drinkEach(drink, Array<flavor>)` function that takes a `drink` function and applies it to array of passed beverages. You might want to check that drink function was called exact number of times. You can do that with this test suite:

```js
describe('drinkEach', () => {
  it('drinks each drink', () => {
    let drink = jest.fn();
    drinkEach(drink, ['lemon', 'octopus']);
    expect(drink).toHaveBeenCalledTimes(2);
  });
});
```

### `.toHaveBeenCalledWith(arg1, arg2, ...)`

Use `.toHaveBeenCalledWith` to ensure that a mock function was called with specific
arguments.

For example, let's say that you can register a beverage with a `register` function, and `applyToAll(f)` should apply the function `f` to all registered beverages. To make sure this works, you could write:

```js
describe('beverage registration', () => {
  it('applies correctly to orange La Croix', () => {
    let beverage = new LaCroix('orange');
    register(beverage);
    let f = jest.fn();
    applyToAll(f);
    expect(f).toHaveBeenCalledWith(beverage);
  });
});
```

### `.toHaveBeenLastCalledWith(arg1, arg2, ...)`

If you have a mock function, you can use `.toHaveBeenLastCalledWith` to test what arguments it was last called with. For example, let's say you have a `applyToAllFlavors(f)` function that applies `f` to a bunch of flavors, and you want to ensure that when you call it, the last flavor it operates on is `'mango'`. You can write:

```js
describe('applying to all flavors', () => {
  it('does mango last', () => {
    let drink = jest.fn();
    applyToAllFlavors(drink);
    expect(drink).toHaveBeenLastCalledWith('mango');
  });
});
```

### `.toBeCloseTo(number, numDigits)`

Using exact equality with floating point numbers is a bad idea. Rounding means that intuitive things fail. For example, this test fails:

```js
describe('adding numbers', () => {
  it('works sanely with simple decimals', () => {
    expect(0.2 + 0.1).toBe(0.3); // Fails!
  });
});
```

It fails because in JavaScript, `0.2 + 0.1` is actually `0.30000000000000004`. Sorry.

Instead, use `.toBeCloseTo`. Use `numDigits` to control how many digits after the decimal point to check. For example, if you want to be sure that `0.2 + 0.1` is equal to `0.3` with a precision of 5 decimal digits, you can use this test:

```js
describe('adding numbers', () => {
  it('works sanely with simple decimals', () => {
    expect(0.2 + 0.1).toBeCloseTo(0.3, 5);
  });
});
```

The default for `numDigits` is 2, which has proved to be a good default in most cases.

### `.toBeDefined()`

Use `.toBeDefined` to check that a variable is not undefined. For example, if you just want to check that a function `fetchNewFlavorIdea()` returns *something*, you can write:

```js
describe('fetching new flavor ideas', () => {
  it('returns something', () => {
    expect(fetchNewFlavorIdea()).toBeDefined();
  });
});
```

You could write `expect(fetchNewFlavorIdea()).not.toBe(undefined)`, but it's better practice to avoid referring to `undefined` directly in your code.

### `.toBeFalsy()`

Use `.toBeFalsy` when you don't care what a value is, you just want to ensure a value is false in a boolean context. For example, let's say you have some application code that looks like:

```js
drinkSomeLaCroix();
if (!getErrors()) {
  drinkMoreLaCroix();
}
```

You may not care what `getErrors` returns, specifically - it might return `false`, `null`, or `0`, and your code would still work. So if you want to test there are no errors after drinking some La Croix, you could write:

```js
describe('drinking some La Croix', () => {
  it('does not lead to errors', () => {
    drinkSomeLaCroix();
    expect(getErrors()).toBeFalsy();
  });
});
```

In JavaScript, there are six falsy values: `false`, `0`, `''`, `null`, `undefined`, and `NaN`. Everything else is truthy.

### `.toBeGreaterThan(number)`

To compare floating point numbers, you can use `toBeGreaterThan`. For example, if you want to test that `ouncesPerCan()` returns a value of more than 10 ounces, write:

```js
describe('ounces per can', () => {
  it('is more than 10', () => {
    expect(ouncesPerCan()).toBeGreaterThan(10);
  });
});
```

### `.toBeGreaterThanOrEqual(number)`

To compare floating point numbers, you can use `toBeGreaterThanOrEqual`. For example, if you want to test that `ouncesPerCan()` returns a value of at least 12 ounces, write:

```js
describe('ounces per can', () => {
  it('is at least 12', () => {
    expect(ouncesPerCan()).toBeGreaterThanOrEqual(12);
  });
});
```

### `.toBeLessThan(number)`

To compare floating point numbers, you can use `toBeLessThan`. For example, if you want to test that `ouncesPerCan()` returns a value of less than 20 ounces, write:

```js
describe('ounces per can', () => {
  it('is less than 20', () => {
    expect(ouncesPerCan()).toBeLessThan(20);
  });
});
```

### `.toBeLessThanOrEqual(number)`

To compare floating point numbers, you can use `toBeLessThanOrEqual`. For example, if you want to test that `ouncesPerCan()` returns a value of at most 12 ounces, write:

```js
describe('ounces per can', () => {
  it('is at most 12', () => {
    expect(ouncesPerCan()).toBeLessThanOrEqual(12);
  });
});
```

### `.toBeInstanceOf(Class)`

Use `.toBeInstanceOf(Class)` to check that an object is an instance of a class. This matcher uses `instanceof` underneath.

```js
class A {}

expect(new A()).toBeInstanceOf(A);
expect(() => {}).toBeInstanceOf(Function);
expect(new A()).toBeInstanceOf(Function); // throws
```

### `.toBeNull()`

`.toBeNull()` is the same as `.toBe(null)` but the error messages are a bit nicer. So use `.toBeNull()` when you want to check that something is null.

```js
function bloop() {
  return null;
}

describe('bloop', () => {
  it('returns null', () => {
    expect(bloop()).toBeNull();
  });
})
```

### `.toBeTruthy()`

Use `.toBeTruthy` when you don't care what a value is, you just want to ensure a value is true in a boolean context. For example, let's say you have some application code that looks like:

```js
drinkSomeLaCroix();
if (thirstInfo()) {
  drinkMoreLaCroix();
}
```

You may not care what `thirstInfo` returns, specifically - it might return `true` or a complex object, and your code would still work. So if you just want to test that `thirstInfo` will be truthy after drinking some La Croix, you could write:

```js
describe('drinking some La Croix', () => {
  it('leads to having thirst info', () => {
    drinkSomeLaCroix();
    expect(thirstInfo()).toBeTruthy();
  });
});
```

In JavaScript, there are six falsy values: `false`, `0`, `''`, `null`, `undefined`, and `NaN`. Everything else is truthy.

### `.toBeUndefined()`

Use `.toBeUndefined` to check that a variable is undefined. For example, if you want to check that a function `bestDrinkForFlavor(flavor)` returns `undefined` for the `'octopus'` flavor, because there is no good octopus-flavored drink:

```js
describe('the best drink', () => {
  it('for octopus flavor is undefined', () => {
    expect(bestDrinkForFlavor('octopus')).toBeUndefined();
  });
});
```

You could write `expect(bestDrinkForFlavor('octopus')).toBe(undefined)`, but it's better practice to avoid referring to `undefined` directly in your code.

### `.toContain(item)`

Use `.toContain` when you want to check that an item is in a list. For testing the items in the list, this uses `===`, a strict equality check.

For example, if `getAllFlavors()` returns a list of flavors and you want to be sure that `lime` is in there, you can write:

```js
describe('the list of all flavors', () => {
  it('contains lime', () => {
    expect(getAllFlavors()).toContain('lime');
  });
});
```

### `.toContainEqual(item)`

Use `.toContainEqual` when you want to check that an item is in a list.
For testing the items in the list, this  matcher recursively checks the equality of all fields, rather than checking for object identity.

```js
describe('my beverage', () => {
  it('is delicious and not sour', () => {
    const myBeverage = {delicious: true, sour: false};
    expect(myBeverages()).toContainEqual(myBeverage);
  });
});
```

### `.toEqual(value)`

Use `.toEqual` when you want to check that two objects have the same value. This matcher recursively checks the equality of all fields, rather than checking for object identity. For example, `toEqual` and `toBe` behave differently in this test suite, so all the tests pass:

```js
const can1 = {
  flavor: 'grapefruit',
  ounces: 12,
};
const can2 = {
  flavor: 'grapefruit',
  ounces: 12,
};

describe('the La Croix cans on my desk', () => {
  it('have all the same properties', () => {
    expect(can1).toEqual(can2);
  });
  it('are not the exact same can', () => {
    expect(can1).not.toBe(can2);
  });
});
```

### `.toHaveLength(number)`

Use `.toHaveLength` to check that an object has a `.length` property and it is set to a certain numeric value.

This is especially useful for checking arrays or strings size.

```js
expect([1, 2, 3]).toHaveLength(3);
expect('abc').toHaveLength(3);
expect('').not.toHaveLength(5);
```

### `.toMatch(regexp)`

Use `.toMatch` to check that a string matches a regular expression.

For example, you might not know what exactly `essayOnTheBestFlavor()` returns, but you know it's a really long string, and the substring `grapefruit` should be in there somewhere. You can test this with:

```js
describe('an essay on the best flavor', () => {
  it('mentions grapefruit', () => {
    expect(essayOnTheBestFlavor()).toMatch(/grapefruit/);
    expect(essayOnTheBestFlavor()).toMatch(new RegExp('grapefruit'));
  })
})
```

This matcher also accepts a string, which it will try to match:

```js
describe('grapefruits are healthy', () => {
  it('grapefruits are a fruit', () => {
    expect('grapefruits').toMatch('fruit');
  })
})
```

### `.toMatchObject(object)`

Use `.toMatchObject` to check that a javascript object matches a subset of the properties of an object.

```js
const houseForSale = {
	bath: true,
	kitchen: {
		amenities: ['oven', 'stove', 'washer'],
		area: 20,
		wallColor: 'white'
	},
  bedrooms: 4
};
const desiredHouse = {
	bath: true,
	kitchen: {
		amenities: ['oven', 'stove', 'washer'],
		wallColor: 'white'
	}
};

describe('looking for a new house', () => {
	it('the house has my desired features', () => {
		expect(houseForSale).toMatchObject(desiredHouse);
	});
});
```


### `.toMatchSnapshot(?string)`

This ensures that a value matches the most recent snapshot. Check out [the React + Jest tutorial](https://facebook.github.io/jest/docs/tutorial-react.html) for more information on snapshot testing.

You can also specify an optional snapshot name. Otherwise, the name is inferred
from the test.

*Note: While snapshot testing is most commonly used with React components, any
serializable value can be used as a snapshot.*

### `.toThrow()`

Use `.toThrow` to test that a function throws when it is called. For example, if we want to test that `drinkFlavor('octopus')` throws, because octopus flavor is too disgusting to drink, we could write:

```js
describe('drinking flavors', () => {
  it('throws on octopus', () => {
    expect(() => {
      drinkFlavor('octopus');
    }).toThrow();
  });
});
```

If you want to test that a specific error gets thrown, use `.toThrowError`.

### `.toThrowError(error)`

Use `.toThrowError` to test that a function throws a specific error when it
is called. The argument can be a string for the error message, a class for the error, or a regex that should match the error. For example, let's say you have a `drinkFlavor` function that throws whenever the flavor is `'octopus'`, and is coded like this:

```js
function drinkFlavor(flavor) {
  if (flavor == 'octopus') {
    throw new DisgustingFlavorError('yuck, octopus flavor');
  }
  // Do some other stuff
}
```

We could test this error gets thrown in several ways:

```js
describe('drinking flavors', () => {
  it('throws on octopus', () => {
    function drinkOctopus() {
      drinkFlavor('octopus');
    }
    // Test the exact error message
    expect(drinkOctopus).toThrowError('yuck, octopus flavor');

    // Test that the error message says "yuck" somewhere
    expect(drinkOctopus).toThrowError(/yuck/);

    // Test that we get a DisgustingFlavorError
    expect(drinkOctopus).toThrowError(DisgustingFlavorError);
  });
});
```

If you don't care what specific error gets thrown, use `.toThrow`.

### `.toThrowErrorMatchingSnapshot()`

Use `.toThrowErrorMatchingSnapshot` to test that a function throws a error matching the most recent snapshot when it is called. For example, let's say you have a `drinkFlavor` function that throws whenever the flavor is `'octopus'`, and is coded like this:

```js
function drinkFlavor(flavor) {
  if (flavor == 'octopus') {
    throw new DisgustingFlavorError('yuck, octopus flavor');
  }
  // Do some other stuff
}
```

The test for this function will look this way:

```js
describe('drinking flavors', () => {
  it('throws on octopus', () => {
    function drinkOctopus() {
      drinkFlavor('octopus');
    }

    expect(drinkOctopus).toThrowErrorMatchingSnapshot();
  });
});
```

And it will generate the following snapshot:

```
exports[`drinking flavors throws on octopus 1`] = `"yuck, octopus flavor"`;
```

Check out [React Tree Snapshot Testing](http://facebook.github.io/jest/blog/2016/07/27/jest-14.html) for more information on snapshot testing.


### Extending Jest Matchers

Using descriptive matchers will help your tests be readable and maintainable. Jest has a simple API for adding your own matchers. Here is an example of adding a matcher:

```js
const five = require('five');

expect.extend({
  toBeNumber(received, actual) {
    const pass = received === actual;
    const message =
      () => `expected ${received} ${pass ? 'not ' : ''} to be ${actual}`;
    return {message, pass};
  }
});

describe('toBe5', () => {
  it('matches the number 5', () => {
    expect(five()).toBeNumber(5);
    expect('Jest').not.toBeNumber(5);
  });
});
```

Jest will give your matchers context to simplify coding. The following can be found on `this` inside a custom matcher:

#### `this.isNot`

A boolean to let you know this matcher was called with the negated `.not` modifier allowing you to flip your assertion.

#### `this.utils`

There are a number of helpful tools exposed on `this.utils` primarily consisting of the exports from [`jest-matcher-utils`](https://github.com/facebook/jest/tree/master/packages/jest-matcher-utils).

The most useful ones are `matcherHint`, `printExpected` and `printReceived` to format the error messages nicely. For example, take a look at the implementation for the `toBe` matcher:

```js
const diff = require('jest-diff');
expect.extend({
  toBe(received, expected) {
    const pass = received === expected;

    const message = pass
      ? () => this.utils.matcherHint('.not.toBe') + '\n\n' +
        `Expected value to not be (using ===):\n` +
        `  ${this.utils.printExpected(expected)}\n` +
        `Received:\n` +
        `  ${this.utils.printReceived(received)}`
      : () => {
        const diffString = diff(expected, received, {
          expand: this.expand,
        });
        return this.utils.matcherHint('.toBe') + '\n\n' +
        `Expected value to be (using ===):\n` +
        `  ${this.utils.printExpected(expected)}\n` +
        `Received:\n` +
        `  ${this.utils.printReceived(received)}` +
        (diffString ? `\n\nDifference:\n\n${diffString}` : '');
      };

    return {actual: received, message, pass};
  },
});
```

This will print something like this:

```
  expect(received).toBe(expected)

    Expected value to be (using ===):
      "banana"
    Received:
      "apple"
```

When an assertion fails, the error message should give as much signal as necessary to the user so they can resolve their issue quickly. It's usually recommended to spend a lot of time crafting a great failure message to make sure users of your custom assertions have a good developer experience.

### Asymmetric Matchers

Sometimes you don't want to check equality of entire object. You just need to assert that value is not empty or has some expected type. For example, we want to check the shape of some message entity:

```
  expect({
    timestamp: 1480807810388,
    text: 'Some text content, but we care only about *this part*'
  }).toEqual({
    timestamp: expect.any(Number),
    text: expect.stringMatching('*this part*')
  });
```

There some special values with specific comparing behavior that you can use as a part of expectation. They are useful for asserting some types of data, like timestamps, or long text resources, where only part of it is important for testing. Currently, Jest has the following asymmetric matchers:

  * `expect.anything()` - matches everything, except `null` and `undefined`
  * `expect.any(<constructor>)` - checks, that actual value is instance of provided `<constructor>`.
  * `expect.objectContaining(<object>)` - compares only keys, that exist in provided object. All other keys of `actual` value will be ignored.
  * `expect.arrayContaining(<array>)` - checks that all items from the provided `array` are exist in `actual` value. It allows to have more values in `actual`.
  * `expect.stringMatching(<string|Regexp>)` - checks that actual value has matches of provided expectation.

These expressions can be used as an argument in `.toEqual` and `.toBeCalledWith`:

```
  expect(callback).toEqual(expect.any(Function));

  expect(mySpy).toBeCalledWith(expect.any(Number), expect.any(String))
```

They can be also used as object keys and may be nested into each other:

```
  expect(myObject).toEqual(expect.objectContaining({
    items: expect.arrayContaining([
      expect.any(Number)
    ])
  }));
```

The example above will match the following object. Array may contain more items, as well as object itself may also have some extra keys:

```
{
  items: [1]
}
```