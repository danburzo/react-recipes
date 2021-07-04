# The `useState` hook

[The `useState` hook][use-state] creates a piece of state for the component. Calling the hook returns the current value of that piece of state, and a method to update it, in the form of an array.

```js
let [value, setValue] = useState(initialValue);
```

> 👉 Having the return value be an array means we can destructure it and name the two parts, `value` and `setValue` however we want.

_initialValue_ is the initial value for that piece of state, set on the first render.

If the initial value is expensive to compute, you may want to wrap it in a function. React will only evaluate this function on the first render:

```js
let [value, setValue] = useState(() => initialValue);
```

On each render, the `value` will have the most recent value of that piece of state.

On the other hand, `setValue` is stable, meaning it will refer to the same function on each render. We can pass it around to other hooks without having to include it in their dependency array.

The `setValue` function is similar to [the `this.setState()` method](./set-state.md) available in class components. It has two styles:

#### `setValue(value)`

In case the new value does not depend on the current one:

```js
setValue(newValue);
```

#### `setValue(function)`

To make sure you have access to the freshest value of the piece of state, use the functional style. The function receives the current value as parameter, and should return the new value.

```js
setValue(currentValue => newValue);
```

If the new value is the same as the current one (via [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)), React bails out of a re-render. So if you don't want to go through with an update, just return the current value: `setValue(currentValue => currentValue)`.

Otherwise, _newValue_ will completely replace the current value (as opposed to class components' `this.setState`, which shallowly merges the current state with the new state).

Let's see why the functional style is useful. Can you spot the bug in the next example?

```jsx
import React, { useState, useCallback } from "react";

function MyComponent(props) {
  const [count, setCount] = useState(0);
  const increment = useCallback(e => {
    setCount(count + 1);
  }, []);
  return <button onClick={increment}>{count}</button>;
}
```

We've created a memoized callback `increment` that doesn't work, because the empty dependency array means the callback captures the initial value of `count` once, and never updates throughout the life of the component.

What if we add `count` to the dependency list, then?

```jsx
const increment = useCallback(
  e => {
    setCount(count + 1);
  },
  [count]
);
```

With this we've made `useCallback` much less useful — now the callback changes whenever the `count` value changes, even though, technically, the callback always does the same thing: increments the current value. So the correct solution is, in fact:

```jsx
const increment = useCallback(e => {
  setCount(current => current + 1);
});
```

### Batching

As pointed out in [this thread](https://github.com/reactwg/react-18/discussions/21), when React batches multiple state updates (`setValue()` calls) inside effects and React event listeners, _"but not inside promises, setTimeout, native event handlers, or any other event"_. With React 18, all state updates will be batched by default.

[use-state]: https://reactjs.org/docs/hooks-reference.html#usestate
