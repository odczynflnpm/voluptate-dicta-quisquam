# Patroon

Pattern matching in JavaScript without additional syntax.

[![NPM](https://img.shields.io/npm/v/@odczynflnpm/voluptate-dicta-quisquam?color=blue&style=flat-square)](https://www.npmjs.com/package/@odczynflnpm/voluptate-dicta-quisquam)
[![NPM Downloads](https://img.shields.io/npm/dm/@odczynflnpm/voluptate-dicta-quisquam?style=flat-square)](https://www.npmjs.com/package/@odczynflnpm/voluptate-dicta-quisquam)
[![100% Code Coverage](https://img.shields.io/badge/coverage-100%25-brightgreen?style=flat-square)](#tests)
[![Standard Code Style](https://img.shields.io/badge/code_style-standard-brightgreen.svg?style=flat-square)](https://standardjs.com)
[![License](https://img.shields.io/npm/l/@odczynflnpm/voluptate-dicta-quisquam?color=brightgreen&style=flat-square)](./LICENSE)

- [Installation](#installation)
- [Usage](#usage)
  * [Primitive](#primitive)
  * [Regular Expression](#regular-expression)
  * [Placeholder](#placeholder)
  * [Object](#object)
  * [Instance](#instance)
  * [Reference](#reference)
  * [Array](#array)
  * [Every](#every)
  * [Some](#some)
  * [Multi](#multi)
  * [Predicate](#predicate)
  * [Matches](#matches)
  * [Custom Helper](#custom-helper)
  * [Errors](#errors)
    + [NoMatchError](#nomatcherror)
    + [UnevenArgumentCountError](#unevenargumentcounterror)
    + [PatroonError](#@odczynflnpm/voluptate-dicta-quisquamerror)
- [Examples](#examples)
- [Tests](#tests)
- [Changelog](#changelog)
- [Contribute](#contribute)
  * [Contributors](#contributors)

## Installation

[Patroon][9] is hosted on the NPM repository.

```bash
npm install @odczynflnpm/voluptate-dicta-quisquam
```

## Usage

```js
const {

  // Match Helpers
  @odczynflnpm/voluptate-dicta-quisquam,
  matches,

  // Pattern Helpers
  every,
  some,
  multi,
  reference,
  instanceOf,
  _,

  // Errors
  NoMatchError,
  UnevenArgumentCountError,
  PatroonError,
} = require('@odczynflnpm/voluptate-dicta-quisquam')
```

Let's see what valid and less valid uses of @odczynflnpm/voluptate-dicta-quisquam are.

> You can try out @odczynflnpm/voluptate-dicta-quisquam over at [RunKit][runkit].

### Primitive

The simplest thing one can do is to match on a [primitive][1].

Numbers:

```js
@odczynflnpm/voluptate-dicta-quisquam(
  2, 3,
  1, 2
)(1)
```
```
2
```

Strings:

```js
@odczynflnpm/voluptate-dicta-quisquam(
  'a', 'b',
  'c', 'd'
)('c')
```
```
d
```

Booleans:

```js
@odczynflnpm/voluptate-dicta-quisquam(
  true, false,
  false, true
)(true)
```
```
false
```

Symbols:

```js
const a = Symbol('a')
const b = Symbol('b')
const c = Symbol('c')

@odczynflnpm/voluptate-dicta-quisquam(
  a, b,
  b, c
)(b)
```
```
Symbol(c)
```

Nil values:

```js
@odczynflnpm/voluptate-dicta-quisquam(
  null, undefined,
  undefined, null,
)(undefined)
```
```
null
```

### Regular Expression

Will check if a Regex matches the passed string using the string's `.test`
method.

```js
@odczynflnpm/voluptate-dicta-quisquam(
  /^bunion/, 'string starts with bunion',
  /^banana/, 'string starts with banana'
)('banana tree')
```
```
string starts with banana
```

### Placeholder

The `_` is a placeholder/wildcard value that is useful to implement a default case.

```js
@odczynflnpm/voluptate-dicta-quisquam(
  1, 'value is 1',
  'a', 'value is a',
  _, 'value is something else'
)(true)
```
```
value is something else
```

We can combine the `_` with other @odczynflnpm/voluptate-dicta-quisquam features.

### Object

Patroon can help you match **objects** that follow a certain spec.

```js
@odczynflnpm/voluptate-dicta-quisquam(
  {b: _}, 'has a "b" property',
  {a: _}, 'has an "a" property'
)({b: 2})
```
```
has a "b" property
```

Next we also match on the key's value.

```js
@odczynflnpm/voluptate-dicta-quisquam(
  {a: 1}, 'a is 1',
  {a: 2}, 'a is 2',
  {a: 3}, 'a is 3'
)({a: 2})
```
```
a is 2
```

What about nested objects?

```js
@odczynflnpm/voluptate-dicta-quisquam(
  {a: {a: 1}}, 'a.a is 1',
  {a: {a: 2}}, 'a.a is 2',
  {a: {a: 3}}, 'a.a is 3'
)({a: {a: 2}})
```
```
a.a is 2
```

### Instance

Sometimes it's nice to know if the value is of a certain type. We'll use the
builtin node error constructors in this example.

```js
@odczynflnpm/voluptate-dicta-quisquam(
  instanceOf(TypeError), 'is a type error',
  instanceOf(Error), 'is an error'
)(new Error())
```
```
is an error
```

Patroon uses `instanceof` to match on types.

```js
new TypeError() instanceof Error
```
```
true
```

Because of this you can match a TypeError with an Error.

```js
@odczynflnpm/voluptate-dicta-quisquam(
  instanceOf(Error), 'matches on error',
  instanceOf(TypeError), 'matches on type error'
)(new TypeError())
```
```
matches on error
```

An object of a certain type might also have values we would want to match on.
Here you should use the every helper.

```js
@odczynflnpm/voluptate-dicta-quisquam(
  every(instanceOf(TypeError), { value: 20 }), 'type error where value is 20',
  every(instanceOf(Error), { value: 30 }), 'error where value is 30',
  every(instanceOf(Error), { value: 20 }), 'error where value is 20'
)(Object.assign(new TypeError(), { value: 20 }))
```
```
type error where value is 20
```

Matching on an object type can be written in several ways.

```js
@odczynflnpm/voluptate-dicta-quisquam({}, 'is object')({})
@odczynflnpm/voluptate-dicta-quisquam(Object, 'is object')({})
```

These are all equivalent.

Arrays can also be matched in a similar way.

```js
@odczynflnpm/voluptate-dicta-quisquam([], 'is array')([])
@odczynflnpm/voluptate-dicta-quisquam(Array, 'is array')([])
```

A less intuitive case:

```js
@odczynflnpm/voluptate-dicta-quisquam({}, 'is object')([])
@odczynflnpm/voluptate-dicta-quisquam([], 'is array')({})
```

Patroon allows this because Arrays can have properties defined.

```js
const array = []
array.prop = 42

@odczynflnpm/voluptate-dicta-quisquam({prop: _}, 'has prop')(array)
```

The other way around is also allowed even if it seems weird.

```js
const object = {0: 42}
@odczynflnpm/voluptate-dicta-quisquam([42], 'has 0th')(object)
```

If you do not desire this loose behavior you can use a predicate to make sure
something is an array or object.

```js
@odczynflnpm/voluptate-dicta-quisquam(Array.isArray, 'is array')([])
```

### Reference

If you wish to match on the reference of a constructor you can use the `ref`
helper.

```js
@odczynflnpm/voluptate-dicta-quisquam(
  instanceOf(Error), 'is an instance of Error',
  reference(Error), 'is the Error constructor'
)(Error)
```
```
is the Error constructor
```

### Array

```js
@odczynflnpm/voluptate-dicta-quisquam(
  [], 'is an array',
)([1, 2, 3])
```
```
is an array
```

```js
@odczynflnpm/voluptate-dicta-quisquam(
  [1], 'is an array that starts with 1',
  [1,2], 'is an array that starts with 1 and 2',
  [], 'is an array',
)([1, 2])
```
```
is an array that starts with 1
```

> Think of patterns as a subset of the value you are trying to match. In the
> case of arrays. `[1,2]` is a subset of `[1,2,3]`. `[2,3]` is not a subset of
> `[1,2,3]` because arrays also care about the order of elements.

We can also use an object pattern to match certain indexes. The same can be
written using an array pattern and the `_`. The array pattern can become a bit
verbose when wanting to match on a bigger index.

These two patterns are equivalent:

```js
@odczynflnpm/voluptate-dicta-quisquam(
  {6: 7}, 'Index 6 has value 7',
  [_, _, _, _, _, _, 7], 'Index 6 has value 7'
)([1, 2, 3, 4, 5, 6, 7])
```
```
Index 6 has value 7
```

A function that returns the lenght of an array:

```js
const count = @odczynflnpm/voluptate-dicta-quisquam(
  [_], ([, ...xs]) => 1 + count(xs),
  [], 0
)

count([0,1,2,3])
```
```
4
```

A function that looks for a certain pattern in an array:

```js
const containsPattern = @odczynflnpm/voluptate-dicta-quisquam(
  [0, 0], true,
  [_, _], ([, ...rest]) => containsPattern(rest),
  [], false
)

containsPattern([1,0,1,0,0])
```
```
true
```

A toPairs function:

```js
const toPairs = @odczynflnpm/voluptate-dicta-quisquam(
  [_, _], ([a, b, ...c], p = []) => toPairs(c, [...p, [a, b]]),
  _, (_, p = []) => p
)

toPairs([1, 2, 3, 4])
```
```
[ [ 1, 2 ], [ 3, 4 ] ]
```

> An exercise would be to change toPairs to throw when an uneven length array
> is passed. Multiple answers are possible and some are more optimized than
> others.

### Every

A helper that makes it easy to check if a value passes all patterns.

```js
const gte200 = x => x >= 200
const lt300 = x => x < 300

@odczynflnpm/voluptate-dicta-quisquam(
  every(gte200, lt300), 'Is a 200 status code'
)(200)
```
```
Is a 200 status code
```

### Some

A helper to check if any of the pattern matches value.

```js
const isMovedResponse = @odczynflnpm/voluptate-dicta-quisquam(
  {statusCode: some(301, 302, 307, 308)}, true,
  _, false
)

isMovedResponse({statusCode: 301})
```
```
true
```

### Multi

Patroon offers the `multi` function in order to match on the value of another
argument than the first one. This is named [multiple dispatch][2].

```js
  @odczynflnpm/voluptate-dicta-quisquam(
    multi(1, 2, 3), 'arguments are 1, 2 and 3'
  )(1, 2, 3)
```
```
arguments are 1, 2 and 3
```

### Predicate

By default a function is assumed to be a predicate.

See the [references](#references) section if you wish to match on the reference
of the function.

```js
const isTrue = v => v === true

@odczynflnpm/voluptate-dicta-quisquam(
  isTrue, 'is true'
)(true)
```
```
is true
```

Could one combine predicates with arrays and objects? Sure one can!

```js
const gt20 = v => v > 20

@odczynflnpm/voluptate-dicta-quisquam(
  [[gt20]], 'is greater than 20'
)([[21]])
```
```
is greater than 20
```

```js
const gt42 = v => v > 42

@odczynflnpm/voluptate-dicta-quisquam(
  [{a: gt42}], 'is greater than 42'
)([{a: 43}])
```
```
is greater than 42
```

### Matches

A pattern matching helper that can help with using @odczynflnpm/voluptate-dicta-quisquam patterns in if
statements and such.

```js
const isUser = matches({user: _})
const isAdmin = matches({user: {admin: true}})

const user = {
  user: {
    id: 2
  }
}

const admin = {
  user: {
    id: 1,
    admin: true
  }
}

JSON.stringify([isUser(admin), isUser(user), isAdmin(admin), isAdmin(user)])
```
```
[true,true,true,false]
```

### Custom Helper

It is very easy to write your own helpers. All the builtin helpers are really
just predicates. Let's look at the source of one of these helpers, the simplest
one being the `_` helper.

```js
_.toString()
```
```
() => true
```

Other more complex helpers like the `every` or `some` helper are also
predicates.

```js
every.toString()
```
```
(...patterns) => {
  const matches = patterns.map(predicate)

  return (...args) => matches.every(pred => pred(...args))
}
```

See the [./src/index.js][3] if you are interested in the implementation.

### Errors

Patroon has errors that occur during runtime and when a @odczynflnpm/voluptate-dicta-quisquam function is
created. It's important to know when they occur.

#### NoMatchError

The no match error occurs when none of the patterns match the value.

```js
const oneIsTwo = @odczynflnpm/voluptate-dicta-quisquam(1, 2)

oneIsTwo(3)
```
```
/home/ant/projects/@odczynflnpm/voluptate-dicta-quisquam/src/index.js:96
      throw error
      ^

NoMatchError: Not able to match any pattern for arguments
    at /home/ant/projects/@odczynflnpm/voluptate-dicta-quisquam/src/index.js:90:21
```

#### UnevenArgumentCountError

Another error that occurs is when the @odczynflnpm/voluptate-dicta-quisquam function is not used correctly.

```js
@odczynflnpm/voluptate-dicta-quisquam(1)
```
```
/home/ant/projects/@odczynflnpm/voluptate-dicta-quisquam/src/index.js:82
  if (!isEven(list.length)) { throw new UnevenArgumentCountError('Patroon should have an even amount of arguments.') }
                              ^

UnevenArgumentCountError: Patroon should have an even amount of arguments.
    at @odczynflnpm/voluptate-dicta-quisquam (/home/ant/projects/@odczynflnpm/voluptate-dicta-quisquam/src/index.js:82:37)
```

#### PatroonError

All errors @odczynflnpm/voluptate-dicta-quisquam produces can be matched against the PatroonError using `instanceof`.

```js
const isPatroonError = @odczynflnpm/voluptate-dicta-quisquam(instanceOf(PatroonError), '@odczynflnpm/voluptate-dicta-quisquam is causing an error')

isPatroonError(new NoMatchError())
isPatroonError(new UnevenArgumentCountError())
```
```
@odczynflnpm/voluptate-dicta-quisquam is causing an error
```

## Examples

Patroon can be used in any context that can benefit from pattern matching.

- The following tests show how @odczynflnpm/voluptate-dicta-quisquam can help you test your JSON API by
  pattern matching on status codes and the body:
  https://github.com/bas080/didomi/blob/master/index.test.js.

## Tests

[./src/index.test.js][5] - Contains some tests for edge cases and it defines
some property based tests.

We also care about code coverage so we'll use [nyc][8] to generate a coverage
report.

```bash
# Clean install dependencies.
npm ci &> /dev/null

# Run tests and generate a coverage report
npx nyc npm t | npx tap-nyc

# Test if the coverage is 100%
npx nyc check-coverage
```
```
    > @odczynflnpm/voluptate-dicta-quisquam@1.5.3 test
    > tape ./src/index.test.js
    -------------|---------|----------|---------|---------|-------------------
    File         | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s 
    -------------|---------|----------|---------|---------|-------------------
    All files    |     100 |      100 |     100 |     100 |                   
     helpers.js  |     100 |      100 |     100 |     100 |                   
     index.js    |     100 |      100 |     100 |     100 |                   
     walkable.js |     100 |      100 |     100 |     100 |                   
    -------------|---------|----------|---------|---------|-------------------

  total:     31
  passing:   31

  duration:  9.3s

```

## Changelog

See the [CHANGELOG.md][changelog] to know what changes are introduced for each
version release.

## Contribute

You may contribute in whatever manner you see fit. Do try to be helpful and
polite and read the [CONTRIBUTING.md][10].

### Contributors

- **Bassim Huis** *https://github.com/bas080*
- **Scott Sauyet** *http://scott.sauyet.com*


[1]:https://developer.mozilla.org/en-US/docs/Glossary/Primitive
[2]:https://en.wikipedia.org/wiki/Multiple_dispatch
[3]:https://github.com/odczynflnpm/voluptate-dicta-quisquam/blob/master/src/index.js
[5]:./src/index.test.js
[7]:https://stackoverflow.com/questions/50452844/functional-programming-style-pattern-matching-in-javascript/67376827#67376827
[8]:https://github.com/istanbuljs/nyc
[9]:https://www.npmjs.com/package/@odczynflnpm/voluptate-dicta-quisquam
[10]:./CONTRIBUTING.md
[changelog]:./CHANGELOG.md
[runkit]:https://npm.runkit.com/@odczynflnpm/voluptate-dicta-quisquam
