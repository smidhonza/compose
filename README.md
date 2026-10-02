# @smidhonza/compose

A tiny TypeScript `compose` helper for combining functions from right to left.

Give it a few functions and it gives you one reusable function back. The function on the right runs first, so you can read `compose(f, g)(value)` as `f(g(value))`.

## Install

```sh
npm i @smidhonza/compose
```

## Usage

```ts
import { compose } from '@smidhonza/compose';

const trim = (value: string) => value.trim();
const upper = (value: string) => value.toUpperCase();
const exclaim = (value: string) => `${value}!`;

const shout = compose(exclaim, upper, trim);

console.log(shout(' hello compose '));
// "HELLO COMPOSE!"
```

`shout` is a function you can call again with a different string. Each step receives the result of the function to its right.

The return type follows the steps, even when they change the type of the value:

```ts
const describeLength = compose(
  (length: number) => `${length} characters`,
  (value: string) => value.length,
);

const description = describeLength('compose');
// description is typed as string: "7 characters"
```

## Why?

Instead of nesting calls:

```ts
const result = exclaim(upper(trim(input)));
```

You can name the whole transformation and reuse it:

```ts
const shout = compose(exclaim, upper, trim);
const result = shout(input);
```

If you prefer to start with a value and list the steps from left to right, see [@smidhonza/pipe](https://github.com/smidhonza/pipe).

## Async functions

You can mix synchronous and asynchronous functions. When a step returns a promise, `compose` waits for it before passing the resolved value to the next step:

```ts
const loadUser = async (id: number) => ({ id, name: 'Ada' });
const greet = compose(
  (user: { id: number; name: string }) => `Hello, ${user.name}!`,
  loadUser,
);

const greeting = await greet(1);
// "Hello, Ada!"
```

A synchronous chain still returns a value directly. A chain with an async step returns a `Promise`. If a step sometimes returns a promise, the composed function may return either a value or a promise.

## Types and behavior

`compose` is typed for up to 11 functions. Each function takes one value, and its resolved result must match the input expected by the function to its left.

## Development

```sh
npm test
npm run build
```

## License

ISC
