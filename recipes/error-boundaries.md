# Error Boundaries

Real-world applications are never perfect. 

A component will sometimes receive data it's not expecting, or the user interacts with the component in a way it doesn't know how to handle — and the component breaks.

In older versions of the library, a broken component would compromise React's innards and make it behave oddly, along with cryptic error messages in the browser console. However, React 16 handles component errors a bit differently: instead of trying to push through, it bails out and __removes the entire component tree from the DOM__ to prevent further damage. 

If your whole app is built in one React piece, it just... goes blank.

Luckily, React 16 also comes with a way to control what happens when these errors occur, with something called [Error Boundaries](https://reactjs.org/docs/error-boundaries.html). In fact, you may have noticed this suggestion in the browser console whenever something breaks:

> Consider adding an error boundary to your tree to customize error handling behavior.

A possible blank screen sounds like a good reason to consider it. So what's it all about?

## The `componentDidCatch` lifecycle method

React 16 added a new component [lifecycle method](./lifecycle-methods.md) by the name of [`componentDidCatch`](https://reactjs.org/docs/react-component.html#componentdidcatch). It gets called any time an unhandled error occurs in one of the component's children. When a component implements a `componentDidCatch` method, it becomes an __Error Boundary__. It's called _boundary_ because it stops the errors from bubbling further up the component tree and wreaking havoc throughout the entire app.

So do we add the `componentDidCatch` method to all our components?

Not quite. A component cannot safely catch errors that happen to _itself_, much like we can't catch ourselves when we're falling. Instead, __it only catches errors from its children__, or its children's children, and so on — anything further down the component tree. 

We'll instead create a component whose _only purpose_ is to act as an error boundary to use whenever we may need it:

```jsx
// Our ErrorBoundary component...
class ErrorBoundary extends React.Component {

  componentDidCatch(error, info) {
    // TODO: do something about the error
  }

  render() {
    return this.props.children;
  }
}

// ...and how we'd use it to wrap a component that may break
<ErrorBoundary>
  <FaultyComponent/>
</ErrorBoundary>
```

With this setup in place, any time our `FaultyComponent` goes haywire, the `ErrorBoundary`'s `componentDidCatch` method will be called. It receives the following parameters:

* `error` is the [Error](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error) object;
* `info` is an object from which we can read the `componentStack` string.

### What gets caught by `componentDidCatch`, and what doesn't

The error boundary will catch __errors that happen inside any of its descendants' lifecycle methods__, including their `render` method, their `constructor`, and `setState` calls.

Errors are __not__ caught in:

* asynchronous code like the one inside `setTimeout` or `requestAnimationFrame` callbacks
* event handlers
* server-side rendering

Plus, as we learned earlier, it doesn't catch errors within itself. Rather, when [the boundary itself throws an error](https://twitter.com/_youhadonejob1), the error bubbles to the next closest boundary up the component tree.

Errors in asynchronous code and event handlers don't _need_ to be caught in error boundaries because they won't break React. You can, however, use the regular JavaScript [`try...catch` statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch) inside the handlers to catch possible erorrs.

__Note:__ if an event handler calls `setState`, as they often do, and something breaks as a result of it, it _will_ get caught by the error boundary.

## What to do about errors

So far our `ErrorBoundary` component doesn't do anything useful. Let's make it:

* show a __fallback UI__ instead of the broken component;
* send the errors to a third-party service, such as [Sentry](https://blog.sentry.io/2017/09/28/react-16-error-boundaries), to keep track of them.

![Error Boundary Demo](./assets/error-boundary.png)

The code below walks you through the process of building such a component:

```jsx

// We're going to use the ErrorBoundary's state to keep track
// of whether an error has happened. We start without an error,
// so these are initially null.

const initial_state = {
  error: null,
  info: null
};

class ErrorBoundary extends React.Component {

  constructor(props) {

    super(props);
    
    // establish the initial state
    this.state = initial_state;

    // Bind any event handlers
    this.reset = this.reset.bind(this);
  }
  
  // When an error occurs in one of the child components,
  // the Error Boundary's `componentDidCatch` will be invoked
  // with information about the error. 
  // 
  // We'll put this information on the state,
  // and in the `render()` method we'll read it back
  // and act accordingly.
  componentDidCatch(error, info) {
    this.setState({ error: error, info: info });

    // Let's also forward the error to a third-party service.
    // Below, we're sending it to Sentry:
    Raven.captureException(error, { extra: info });
  }

  // We also want to allow the user to recover from an error,
  // so we add a button in the fallback UI that resets the 
  // error boundary to its initial state (no error).
  // 
  // This causes it to try to re-render the child components
  // and hope they don't break again in the process.
  reset() {
    this.setState(initial_state);
  }

  render() {
    // read the error information from the state
    let { error, info } = this.state;

    // if we have an error, let's render the fallback UI...
    if (error) {
      return (
        <div className='error-boundary'>
          <h1>Something went wrong 🙊</h1>
          <h2>{ error.toString() }</h2>
          <pre>
            { info.componentStack }
          </pre>
          <button onClick={this.reset}>Try again</button>
        </div>
      );
    }

    // ...otherwise, render the child components.
    return this.props.children;
  }
}
```

In addition to capturing errors and rendering the fallback UI, our `ErrorBoundary` component includes a _Try again_ button that clears the error and tries to re-render the component it wraps. 

Depending on the error, the component may break again immediately. But re-mounting a fresh component may sometimes solve the problem — if the error was, for example, the result of some aspect of the component's `state` getting corrupted. For at least these cases, letting the user reset the `ErrorBoundary` is better than nothing.

Now, let's put this error boundary to good use. Below we have a `FaultyComponent` that will break when the user presses its only button.

```jsx
class FaultyComponent extends React.Component {

  constructor(props) {
    super(props);

    // notice a conspicuous lack 
    // of state initialization in the constructor

    // bind event handlers
    this.break = this.break.bind(this);
  }

  // We "forgot" to add an initial state,
  // so this method breaks the component.
  break() {
    this.setState(
      prevState => ({ counter: prevState.counter + 1 })
    )
  }

  render() {
    return (
      <div className='faulty-component'>
        <h1>Faulty Component</h1>
        <p>
          Like my human creator, I am prone to errors, 
          but I try my best to keep it cool. 
          I wouldn't push the button below if I were you, though.
        </p>
        <button onClick={this.break}>Click Me</button>
      </div>
    );
  }
}
```

If we were to include the `FaultyComponent` by itself in our app, a press of our rigged button will blow up the entire thing. But if we wrap it in the `ErrorBoundary` we've just created, the boundary will stop the error from propagating, and show the fallback UI:

```jsx
<ErrorBoundary>
  <FaultyComponent/>
</ErrorBoundary>
```

## Where to put your error boundaries

You can generally write a single `ErrorBoundary` component and use it in as many placess as you need it. A single boundary that wraps the entire app can be a starting point, but we could also add a few in major areas of the app that can work independently of one another.

We can go further and make our `ErrorBoundary` a bit more generic, and have it render a fallback UI that we can configure. Maybe in some places we feel like showing a frowny _Bummer!_ message, and that in others a _Quote of the day_ widget is a good replacement for broken functionality. (Reader, it rarely is.)

This is a good fit for the [render prop pattern](./render-prop-pattern.md), with which our setup becomes:

```jsx

// In this particular instance of the ErrorBoundary
// we show the FaultyComponentFallback component 
// when an error happens.
<ErrorBoundary 
  render={
    (error, info, onReset) => 
      <FaultyComponentFallback error={error} info={info} onReset={onReset}/>
  }
>
  <FaultyComponent/>
</ErrorBoundary>
```

To support the pattern we'll make a change to the boundary's `render()` method so that when an error exists, it calls the render prop with the error data.

```jsx
class ErorrBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = initial_state;
    this.reset = this.reset.bind(this);
  }

  componentDidCatch(error, info) {
    this.setState({ error: error, info: info });
  }

  reset() {
    this.setState(initial_state);
  }

  render() {
    let { error, info } = this.props;
    if (error) {
      return this.props.render(error, info, this.reset);
    }
    return this.props.children;
  }
}
```

What if we only _occasionally_ need custom fallback components, and are in general happy with a default one? [`defaultProps` lets us plug missing props](./defaultprops.md), so we'll set up a default `render` prop:

```jsx
ErrorBoundary.defaultProps = {
  render: (error, info, onReset) => 
    <div className='error-boundary'>
      <h1>Something went wrong 🙊</h1>
      <h2>{ error.toString() }</h2>
      <pre>
        { info.componentStack }
      </pre>
      <button onClick={onReset}>Try again</button>
    </div>
}
```

## Conclusion

An `ErrorBoundary` is a good addition to your app's menagerie of general-purpose components. Write it once, put it in a few places, and you're already offering a better experience to your users.
