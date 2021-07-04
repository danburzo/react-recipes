# React Recipes

> __Status__: The introduction of [Hooks](https://reactjs.org/docs/hooks-intro.html) has fundamentally changed how we write React. The official documentation is [currently being reorganized](https://github.com/reactjs/reactjs.org/issues/3308) around a hooks-first approach, and these articles need to similarly shift focus.

## Introduction

[This repository](https://github.com/danburzo/react-recipes/) contains some tried-and-tested ways to work with React that I picked up along the way, and in-depth explanations of how certain aspects of the library work.

The articles assume you have a bit of experience with React, and that you've set it up to use [JSX](https://reactjs.org/docs/introducing-jsx.html) and modern JavaScript features, such as classes and modules. These require a bit of initial setup and although they're not 100% necessary, forgoing JSX and ES6 modules makes for a less-than-stellar development experience. Check out [_Setting up shop_](./recipes/setting-up-shop.md) for a quick way to get started on a React project with all the goodness baked in.

I mostly discuss the plain React API. Some articles do however point to useful libraries and tools when they're easy to pick up and don't introduce too many new concepts. The articles rarely touch on aspects of React style, such as naming, indentation, using arrow functions, et cetera. You can enforce a consistent style in your project with [Prettier](https://prettier.io/), and supplement it with the [Airbnb React/JSX Style Guide](https://github.com/airbnb/javascript/tree/master/react).

Finally, but most importantly, these articles [reflect my current understanding](https://github.com/danburzo/as-we-learn) of how React works. While I strive to give the clearest picture possible, some aspects may still be inaccurate, out of date, or just plain Bad Ideas. Sorry in advance, I'm trying my best! If you spot any errors or dubious claims, please let me know by filing an issue or a pull request.

## Table of contents

### First steps

This section is about getting started with React. I hope to expand this section with more introductory articles.

- [**Setting up shop**](./recipes/setting-up-shop.md) walks you through setting up a React project with JSX and modern JavaScript without losing your soul in the process.

### Mental models

- [**Reading JSX as if it were JavaScript**](./recipes/jsx-to-javascript.md) to get a clearer picture of how React works.
- [**Why immutability is important**](./recipes/immutability.md) explains why you want to keep your data immutable when working with React.

### Life inside a component

This section is about how to build strong React components.

- [**Ways to define components**](./recipes/components.md) walks you through the pros and cons of functional vs. class-based React components.
- [**Ways to use `defaultProps`**](./recipes/defaultprops.md) to make your code clearer.
- [**Using `propTypes`**](./recipes/defaultprops.md) to ensure components receive what they expect.
- [**Controlled, uncontrolled, and somewhere in between**](./recipes/controlled-uncontrolled.md)

#### Class components

- [**The component lifecycle**](./recipes/lifecycle.md) describes the methods available on a class component, how they're invoked and what they're good at.
- [**Use the best `setState` style for the job**](./recipes/set-state.md) when updating your component's state.

#### Function components

#### Hooks

Notes on the various hooks available in React.

- [**Hooks: an overview**](./recipes/hooks.md)

##### Built-in hooks

- [**`useState`**](./recipes/use-state.md)
- [**`useEffect`**](./recipes/use-effect.md)

##### Custom hooks

- [**Writing custom hooks**](./recipes/custom-hooks.md)
- [**`usePrevious`**](./recipes/custom-hooks/use-previous.md) — keep references to the previous value of thigs
- [**`useBounds`**](./recipes/custom-hooks/use-bounds.md) — a few ways to measure DOM elements

### The component and the outside world

This section discusses how our component can interact with things outside its boundaries.

- [**Handling events outside the component**](./recipes/outside-events.md) the React way with the help of a small library.
- [**Rendering things outside the component**](./recipes/portals.md) with Portals.
- [**Embedding React components in an existing app**](./recipes/inserting-components.md) shows how to turn some parts of your app over to React.

### How components talk to each other

In this section we explore some patterns of communication between components, and how to combine them like Lego bricks to build up our app.

- [**How components talk to each other**](./recipes/component-communication.md) is an intro to how React components compose.
- [**The `property` pattern for callbacks**](./recipes/property-pattern.md) helps you handle events more elegantly.
- [**Passing React components via props**](./recipes/passing-components.md) outlines some component composition strategies.
- [**Passing props to `this.props.children`**](./recipes/children-props.md) using `React.Children.map` and `React.cloneElement`.

#### Passing data to children

Passing children to components is one of the basic ways to compose our application's element tree. A component that receives `children` it knows little about may want to share information with them nonetheless. The React API affords a few related methods to do that.

- [**Passing data to children: an overview**](./recipes/children-data-overview.md)
- [**Extending children with `React.Children` and `React.cloneElement`**](./recipes/extending-children.md)
- [**Higher-Order Components**](./recipes/hoc.md)
- [**Render props**](./recipes/render-props.md)

### Special-purpose components

The React API allows us to write some useful generic components for our app.

- [**Error Boundaries**](./recipes/error-boundaries.md) will stop a component that crashed from breaking your whole app.

### Performance

- [**An introduction to performance**](./recipes/performance.md)
- [**Making things simpler and faster with memoization**](./recipes/memoization.md)
- [**Preventing useless updates with `React.PureComponent` and `React.memo`**](./pure.md)
- [**The `useMemo` hook**](./use-memo.md)
- [**Pure component caveats**](./recipes/pure-caveats.md) shows you some scenarios where you're better off not using `PureComponent`.
- [**`why-did-you-update` is your new best friend**](./recipes/why-did-you-update.md) that lets you know when your components are updating uselessly.
- [**DOM Frugality: `React.Fragment` and returning arrays**](./recipes/fragments.md)

## Further reading

[The official React website](https://reactjs.org/) has comprehensive guides, tutorials, and links to useful tools. React has one of the best documentations around. Spend a fortnight reading the guides cover to cover and you'll get a much firmer grasp on how to use React efficiently.

On his [Overreacted](https://overreacted.io/) blog, Dan Abramov goes on interesting deep-dives into how React works under the hood.

Then there are other React pattern repositories you might find interesting:

- [reactpatterns.com](http://reactpatterns.com/)
- [react-in-patterns](https://github.com/krasimir/react-in-patterns)
- [react-playbook](https://github.com/kylpo/react-playbook)
- [react-bits](https://github.com/vasanthk/react-bits)

Finally, some assorted articles from around the web:

- [React Tutorial: A Comprehensive Guide to learning React.js in 2018](https://tylermcginnis.com/reactjs-tutorial-a-comprehensive-guide-to-building-apps-with-react/) by Tyler McGinnis
- [Writing good component API](https://sid.studio/component-api/) by Siddharth Kshetrapal
