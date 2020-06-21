---
id: immutabilityMiddleware
title: Immutability Middleware
sidebar_label: Immutability Middleware
hide_title: true
---

# Immutability Middleware

A port of the [`redux-immutable-state-invariant`](https://github.com/leoasis/redux-immutable-state-invariant) middleware, customized for use with Redux Toolkit. Any detected mutations will be thrown as errors.

This middleware is added to the store by default by [`configureStore`](./configureStore.md) and [`getDefaultMiddleware`](./getDefaultMiddleware.md).

You can customize the behavior of this middleware by passing any of the supported options as the `immutableCheck` value for `getDefaultMiddleware`.

## Options

```ts
type IsImmutableFunc = (value: any) => boolean

interface ImmutableStateInvariantMiddlewareOptions {
  /**
    Callback function to check if a value is considered to be immutable.
    This function is applied recursively to every value contained in the state.
    The default implementation will return true for primitive types 
    (like numbers, strings, booleans, null and undefined).
   */
  isImmutable?: IsImmutableFunc
  /** 
    An array of dot-separated path strings that match named nodes from 
    the root state to ignore when checking for immutability.
    Defaults to undefined
   */
  ignoredPaths?: string[]
  /** Print a warning if checks take longer than N ms. Default: 32ms */
  warnAfter?: number
  // @deprecated. Use ignoredPaths
  ignore?: string[]
}
```

## Exports

### `createImmutableStateInvariantMiddleware`

Creates an instance of the immutability check middleware, with the given options.

You will most likely not need to call this yourself, as `getDefaultMiddleware` already does so.

Example:

```js
import {
  createSlice,
  configureStore,
  createImmutableStateInvariantMiddleware
} from '@reduxjs/toolkit'

const exampleSlice = createSlice({
  name: 'example',
  initialState: {
    user: 'will track changes',
    ignoredPath: 'single level',
    ignoredNested: {
      one: 'one',
      two: 'two'
    }
  },
  reducers: {}
})

const immutableInvariantMiddleware = createImmutableStateInvariantMiddleware({
  ignoredPaths: ['ignoredPath', 'ignoredNested.one', 'ignoredNested.two']
})

const store = configureStore({
  reducer: exampleSlice.reducer,
  // Note that this will replace all default middleware
  middleware: [immutableInvariantMiddleware]
})
```

### `isImmutableDefault`

Default implementation of the "is this value immutable?" check. Currently implemented as:

```js
return (
  typeof value !== 'object' || value === null || typeof value === 'undefined'
)
```

This will return true for primitive types (like numbers, strings, booleans, null and undefined)