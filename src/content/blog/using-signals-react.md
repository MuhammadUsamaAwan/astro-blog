---
author: Muhammad Usama
pubDatetime: 2023-11-09T08:47:48.651Z
title: Using Signals to Optimize React Performance
postSlug: using-signals-to-optimize-react-performance
featured: true
draft: false
tags:
  - react-js
description: Hooks in React are hard to use correctly and even hard to use them and in a performant way. Many newer frontend frameworks are adapting signals for reactive and performance. In this article we will look at how you can use signals in React to optimize perfomance and manage state in your application.
---

Hooks in React are hard to use correctly and even hard to use them and in a performant way. Many newer frontend frameworks are adapting signals for reactive and performance. In this article we will look at how you can use signals in React to optimize perfomance and manage state in your application.

## Table of contents

## Using React Hooks

To demonstrate React Hooks vs Signal. I have created a [repository](https://github.com/MuhammadUsamaAwan/react-signals) with two simple projects using Vite, the project in the folder `without-signals` contains code that uses React hooks and the `with-signals` contains code using that uses signals.

Lets take a look a the code using React hooks.

```ts
// file: App.tsx

import { useState } from "react"
import Component1 from "./Component1"
import Component2 from "./Component2"
import Component3 from "./Component3"

export default function App() {
  console.log("rendered app")
  const [state, setState] = useState(true)

  return (
    <>
      <Component1 state={state} />
      <Component2 state={state} />
      <Component3 />
      <button onClick={() => setState(!state)}>Toggle State</button>
    </>
  )
}
```

```ts
// file: Component1.tsx

export default function Component1({ state }: { state: boolean}) {
  console.log("rendered Component1")
  return (
    <div>Component1 state {state ? 'true' : 'false'}</div>
  )
}
```

```ts
// file: Component2.tsx

export default function Component2({ state }: { state: boolean}) {
  console.log("rendered Component2")
  return (
    <div>Component2 state {state ? 'true' : 'false'}</div>
  )
}
```

```ts
// file: Component3.tsx

export default function Component3() {
  console.log("rendered Component3")
  return (
    <div>Component3</div>
  )
}
```

As you can see in the code the `App` is rendering three components `Component1`, `Component2` and `Component3`. The `state` is defined in the `App` and is passed to `Component1` and `Component2` as a props. All components have `console.log` to show when the component is rendered. There is also a button which is used to toggle the state to see the render behavior of components.

On initial load we see this,

```ts
// browser console

rendered app             App.tsx:7
rendered Component1      Component1.tsx:2
rendered Component2      Component2.tsx:2
rendered Component3      Component3.tsx:2
```

Which is completely fine, as on first load all components will be rendered. But when the button is pressed to toggle state we see this,

```ts
// browser console

rendered app             App.tsx:7
rendered Component1      Component1.tsx:2
rendered Component2      Component2.tsx:2
rendered Component3      Component3.tsx:2
```

So what happened, It makes sense that Component1 and Component2 is re-rendered because it is using the state, but why Component3. Well because App is re-rendered (due to state change) and this causes every component in app to re-render regardless if that component is using the state or now.

While this can be solved by using Context and Memoization, it's relatively hard and tedicious. Now let's see how the code using signals.

## Using Signals

First we need to install a signals library

```sh
# npm
npm i @preact/signals

# yarn
yarn add @preact/signals

# pnpm
pnpm add @preact/signals

# bun
bun add @preact/signals
```

Lets take a look a the code using signals.

```ts
// file: App.tsx

import { signal } from '@preact/signals-react';
import Component1 from './Component1';
import Component2 from './Component2';
import Component3 from './Component3';

export const state = signal(true);

export default function App() {
  console.log('rendered app');

  return (
    <>
      <Component1 />
      <Component2 />
      <Component3 />
      <button onClick={() => (state.value = !state.value)}>Toggle State</button>
    </>
  );
}
```

```ts
// file: Component1.tsx

import { state } from "./App"

export default function Component1() {
  console.log("rendered Component1")
  return (
    <div>Component1 state {state.value ? 'true' : 'false'}</div>
  )
}
```

```ts
// file: Component2.tsx

import { state } from "./App"

export default function Component2() {
  console.log("rendered Component2")
  return (
    <div>Component2 state {state.value ? 'true' : 'false'}</div>
  )
}
```

```ts
// file: Component3.tsx

export default function Component3() {
  console.log("rendered Component3")
  return (
    <div>Component3</div>
  )
}
```

As you can see we have imported `signal` in `App` and create a new signal named `state` with initial value false. You can already see the first advantage of using signal, you can just export signal from anywhere and use it inside anywhere even in normal functions which isn't possible when using hooks.

On initial load we see this,

```ts
// browser console

rendered app             App.tsx:11
rendered Component1      Component1.tsx:4
rendered Component2      Component2.tsx:4
rendered Component3      Component3.tsx:4
```

Which is completely fine, as on first load all components will be rendered. But when the button is pressed to toggle state we see this,

```ts
// browser console

rendered Component1      Component1.tsx:4
rendered Component2      Component2.tsx:4
```

As you can see only the components will were using the state gets re-rendered and nothing else. And this is done without using any selectors, wrappers, or anything else.

## Outro

Signals can be an easy and performant way to use state management in React. You can learn more about signals and it's other useful API like `effect(fn)`, `computed(fn)` by visiting their [Github](https://github.com/preactjs/signals).
