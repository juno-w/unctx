# 🍦 unctx

> Composition-api in Vanilla js

[![npm version][npm-v-src]][npm-v-href]
[![npm downloads][npm-dm-src]][npm-dm-href]
[![package phobia][packagephobia-src]][packagephobia-href]
[![bundle phobia][bundlephobia-src]][bundlephobia-href]
[![codecov][codecov-src]][codecov-href]

## What is it?

[Vue.js](https://vuejs.org) introduced an amazing pattern called [Composition API](https://v3.vuejs.org/guide/composition-api-introduction.html) that allows organizing complex logic by splitting it into reusable functions and grouping in logical order. `unctx` allows easily implementing composition api pattern in your javascript libraries without hassle.

## Integration

In you **awesome** library:

```bash
yarn add unctx
# or
npm install unctx
```

```js
import { createContext } from 'unctx'

const ctx = createContext()

export const useAwesome = ctx.use

// ...
ctx.call({ test: 1 }, () => {
  // This is similar to vue setup function
  // Any function called here, can use `useAwesome` to get { test: 1 }
})
```

User code:

```js
import { useAwesome } from 'awesome-lib'

// ...
function setup() {
  const ctx = useAwesome()
}
```

## Using Namespaces

To avoid issues with multiple version of library, `unctx` provides a safe global namespace to access context by key (kept in [`globalThis`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/globalThis)). **Important:** Please use a verbose name for key to avoid conflict with other js libraries. Using npm package name is recommended. Using symbols has no effect since it still causes multiple context issue.

```js
import { useContext, getContext } from 'unctx'

const useAwesome = useContext('awesome-lib')

// or
// const awesomeContext = getContext('awesome-lib')
```

You can also create your own internal namespace with `createNamespace` utility for more advanced use cases.

## Typescript

A generic type exists on all utilities to be set for instance/context type:

```ts
// Return type of useAwesome is Awesome | null
const { use: useAwesome } = createContext<Awesome>()
```

## Under the hood

Composition of functions is possible using temporary context injection. When calling `ctx.call(instance, cb)`, `instance` argument will be stored in a temporary variable then `cb` is called. Any function inside `cb`, can then implicitly access instance by using `ctx.use` (or `useAwesome`)

## Pitfalls

**context can be only used before first await**:

 To avoid leaking context, `call` method synchronously sets context and unset it as soon as possible. Because of this, `useAwesome` should happen before first `await` call and reused if necessary.

```js
async function setup() {
  const awesome = useAwesome()
  await someFunction()
  // useAwesome() returns null here
}
```

**`Context conflict` error**:

In your library, you should only keep one `call()` running at a time (unless calling with same reference for first argument)

For instance this makes an error:

```js
ctx.call({ test: 1 }, () => {
  ctx.call({ test: 2 }, () => {
    // Throws error!
  })
})
```

## License

MIT. Made with 💖

<!-- Refs -->
[npm-v-src]: https://flat.badgen.net/npm/v/unctx/latest
[npm-v-href]: https://npmjs.com/package/unctx

[npm-dm-src]: https://flat.badgen.net/npm/dm/unctx
[npm-dm-href]: https://npmjs.com/package/unctx

[packagephobia-src]: https://flat.badgen.net/packagephobia/install/unctx
[packagephobia-href]: https://packagephobia.now.sh/result?p=unctx

[bundlephobia-src]: https://flat.badgen.net/bundlephobia/min/unctx
[bundlephobia-href]: https://bundlephobia.com/result?p=unctx

[codecov-src]: https://flat.badgen.net/codecov/c/github/unjs/unctx/master
[codecov-href]: https://codecov.io/gh/unjs/unctx
