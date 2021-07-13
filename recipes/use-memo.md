# The `useMemo` hook

[The `useMemo` hook][use-memo] is a performance optimization that returns a memoized value for a function.

```js
const memoizedValue = useMemo(() => sumPrimeNumbers(min, max), [min, max]);
```

The array `[a, b]` represents the array of dependencies for which the return of the memoized function may change. Note that while these are not automatically passed to the function, in theory every value referenced inside the memoized function should be listed in the dependency array.

## Example
The below example illustrates how `useMemo` may be used within a component.

```js
import React, { useMemo } from "react";
import ReactDOM from "react-dom";

const sumPrimeNumbers = (min, max) => {
  // computationally expensive calculation
  return min + max;
};

const MyComponent = ({ min, max }) => {
  const sum = useMemo(() => sumPrimeNumbers(min, max), [min, max]);

  return <div>{sum}</div>;
};

ReactDOM.render(
  <MyComponent min={1} max={2} />,
  document.getElementById("container")
);
```

Note that the above example is overly simple and may be a use case for [`React.memo`][react-memo] to memoize the output of the component.

[use-state]: https://reactjs.org/docs/hooks-reference.html#usememo
