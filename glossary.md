# Glossary

The glossary explains some common React jargon in simpler terms. See also [Glossary + Explain Like I'm Five](https://github.com/reactwg/react-18/discussions/46) in the React Working Group.

#### ancestor component

See [component, ancestor](#component-ancestor).

#### Babel

#### callback function

See [function, callback](#function-callback).

#### child component

See [component, child](#component-child).

#### class component

See [component, class](#component-class).

#### descendant component

See [component, descendant](#component-descendant).

#### drilling, prop

Prop drilling refers to passing down a prop from an ancestor to a descendant component by repeatedly passing it from parent to child component. Also called [threading](#threading).

#### comparison, deep

#### comparison, shallow

We make a _comparison_ to tell if two objects are the same. 

In JavaScript simple values (numbers, strings, etc.) that match are equal:

```js
"hello" === "hello"
1 === 1
null === null
undefined === undefined
```

However, Arrays and Objects are only equal if they have the _same reference_ (that is, they point to the same thing), even though they look to have the same values inside.

```js
{ a: 1, b: 2} !== { a:1, b: 2 };
[1, 2, 3] !== [1, 2, 3];

let objA = objB = { a: 1, b: 2};
objA === objB;

let arrA = arrB = [1, 2, 3];
arrA === arrB;
```

A more useful comparison might look at whether the properties of two objects, or the items in two arrays, are the same:

```js
equal({ a:1, b:2 }, {a: 1, b: 2 }) // => true
```

Now the question is, how deep inside objects do we look?

A _shallow comparison_ looks only at the top-level properties to see if they match, hence the name _shallow_:

```js
let objA = { a:1, b:2 };
let objB = { a:1, b:2 };

shallowEqual(objA, objB); // => true
```

```js
let objC = { 
  a:1, 
  b: { 
    ba:1, 
    bb:2 
  } 
};

let objD = { 
  a:1, 
  b: { 
    ba:1, 
    bb:2 
  }
};

shallowEqual(objC, objD); // => false
```

A comparison that looks at nested properties inside objects is called a [deep comparison](#comparison-deep), but it can be slower to check the properties at all levels.

A shallow comparison is thus useful enough, and fast enough, to be used in React components so that we can tell whether anything was added, removed, or updated in the component's `props` or `state`, and decide whether that warrants a re-render.

`React.PureComponent` is, in fact, just a `React.Component` that implements a shallow comparison between the current `props` and the new `props`, and the current `state` and new `state` in the `shouldComponentUpdate` method. 

It makes it important to use [immutable structures](#immutable-structure).

#### component

#### component, ancestor

We say that a component is an _ancestor_ of another component if it's higher up the [component tree](#component-tree). A direct ancestor is usually called a [parent](#component-parent) component. 

The reverse of an ancestor is called a [descendant](#component-descendant). A direct descendant is called a [child](#component-child).

```jsx
<A>
  <B>
    <C/>
  </B>
</A>
```

In the example above, `A` is an ancestor of `C`; conversely, `C` is a descendant of `A`.

In React parents and children communicate via [props](#prop). Ancestors and descendants can use the [context](#context) API to communicate direcly without having to pass props down the component tree from parent to child.

#### component, child

In React parents and children communicate via [props](#prop).

#### component, class

#### component, descendant

We say that a component is a _descendant_ of another component if it's lower down the [component tree](#component-tree). A direct descendant is usually called a [child](#component-child) component. 

The reverse of an descendant is called an [ancestor](#component-ancestor). A direct ancestor is called a [parent](#component-parent).

```jsx
<A>
  <B>
    <C/>
  </B>
</A>
```

In the example above, `C` is an descendant of `A`; conversely, `A` is an ancestor of `C`.

In React parents and children communicate via [props](#prop). Ancestors and descendants can use the [context](#context) API to communicate direcly without having to pass props down the component tree from parent to child.

#### component, functional

#### component, higher-order

#### component, parent

In React parents and children communicate via [props](#prop).

#### component, pure

#### component, stateful

#### component, stateless

#### component tree

Also called _component hierarchy_.

#### context

#### cross-cutting concern

#### deep comparison

See [comparison, deep](#comparison-deep).

#### deep merge

See [merge, deep](#merge-deep).

#### element

#### ES2015

#### ES6

#### event

#### event, native

#### event, synthetic

#### function, callback

#### function, pure

#### function, impure

#### functional component

See [component, functional](#component-functional).

#### higher-order component

See [component, higher-order](#component-higher-order).

#### immutable structure

#### impure function

See [function, impure](#function-impure).

#### JSX

#### lifecycle method

See [method, lifecycle](#method-lifecycle).

#### map

#### memoize

#### merge, shallow

_Merging_ means combining two or more objects. It usually involves a _source_ object and a _destination_ object.

A _shallow merge_ is takes the properties from the source object and copies them over to the _destination_ object, adding to it any new properties and _overwriting_ existing properties with new values.

```js
const source = {
  name: 'New Name',
  address: {
    city: 'Bucharest'
  },
  birthdate: '01/01/1911'
};

const destination = {
  name: 'Old Name',
  address: {
    city: "Cluj-Napoca",
    street: "Museum Square"
  },
  occupation: 'freelancer'
};

shallowMerge(destination, source);

/* => 
{
  name: 'New Name',
  address: {
    city: 'Bucharest'
  },
  occupation: 'freelancer'
}
 */
```

In the example above, the _destination_ object:

* gets a new value for the existing string property `name`;
* gets an entirely new object for the existing object property `address`;
* gets a new property `birthdate`;
* retains its value for the property `occupation`.

It's called a _shallow_ merge because it doesn't merge nested properties; instead it overwrites them altogheter (in the example, the `address` property gets overwritten). A merge that also applies to nested properties is called a [deep merge](#merge-deep).

A React component's `setState(newState)` method _shallowly merges_ `newState` into the component's existing [state](#state-react), so you only need to send the properties you want to change to it.

#### merge, deep

_Merging_ means combining two or more objects. It usually involves a _source_ object and a _destination_ object.

A _deep merge_ is takes the properties from the source object and copies them over to the _destination_ object, adding to it any new properties and _merging_ the old values of existing properties with the new values.

```js
const source = {
  name: 'New Name',
  address: {
    city: 'Bucharest'
  },
  birthdate: '01/01/1911'
};

const destination = {
  name: 'Old Name',
  address: {
    city: "Cluj-Napoca",
    street: "Museum Square"
  },
  occupation: 'freelancer'
};

deepMerge(destination, source);

/* => 
{
  name: 'New Name',
  address: {
    city: 'Bucharest',
    street: "Museum Square"
  },
  occupation: 'freelancer'
}
 */
```

In the example above, the _destination_ object:

* gets a new value for the existing string property `name`;
* gets a value for the existing object property `address` that merges existing value and the new value;
* gets a new property `birthdate`;
* retains its value for the property `occupation`.

It's called a _deep_ merge because also _merges_ nested properties instead of overwriting them altogether as in the case of a [shallow merge](#merge-shallow).

A React component's `setState(newState)` method __does not__ deeply merge `newState` into the component's existing [state](#state-react) (instead, it does so [shallowly](#merge-shallow)). Use [a variant of the `setState` function](./recipes/set-state.md) that allows you to build the new state out based on the existing one to update a nested property:

```js
this.setState(
  previous_state => {
    return {
      address: {
        ...previous_state.address,
        city: 'Bucharest'
      }
    }
  }
)
```

The example above updates the nested `address.city` property of the state. 

#### method, lifecycle

#### mount

_Mounting_ means adding a [component](#component) to the DOM. The opposite operation of removing a component from the DOM is called [unmounting](#unmount).

#### native event

See [event, native](#event-native).

#### parent component

See [component, parent](#component-parent).

#### pure component

See [component, pure](#component-pure).

#### pure function

See [function, pure](#function-pure).

#### prop

#### props

#### property, static

#### React

#### Redux

#### ref

#### shallow comparison

See [comparison, shallow](#comparison-shallow).

#### shallow merge

See [merge, shallow](#merge-shallow).

#### side-effect

#### state

#### state (React)

#### state (Redux)

#### stateful component

See [component, stateful](#component-stateful).

#### stateless component

See [component, stateless](#component-stateless).

#### static property

See [property, static](#property-static).

#### synthetic event

See [event, synthetic](#event-synthetic).

#### threading

See [drilling, prop](#drilling-prop).

#### unmount

_Unmounting_ means removing a [component](#component) to the DOM. The opposite operation of adding a component to the DOM is called [mounting](#mount).

#### Webpack

