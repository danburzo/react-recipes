# The `useInitialRender` hook: distinguish between the initial and subsequent renders

## Implementation

```js
// use-initial-render.js
import { useRef, useEffect } from "react";

const useInitialRender = () => {
  const isInitialrender = useRef(true);
  useEffect(() => {
    isInitialrender.current = false;
  }, []);
  return isInitialrender.current;
};

export default useInitialRender;
```

## Usage

```js
import { useState, useEffect } from "react";
import useInitialRender from "./use-initial-render.js";

const MyComponent = props => {
  const isInitialRender = useInitialRender();
  const [myState, setMyState] = useState(0);
  useEffect(() => {
    if (isInitialRender) {
      // componentDidMount
    }
    // componentDidUpdate
  }, [myState]);
};
```
