# React Lifecycle and Hooks

React has four built-in methods that gets called, in this order, when mounting a component. These are outdated with function components. Hooks lets us split a component based on related pieces rather then split on lifecycle methods.

1. Mounting (adding nodes to the DOM)
2. Updating (altering existing nodes in the DOM)
3. Unmounting (removing nodes from the DOM)
4. Error handling (verifying that your code works and is bug-free)

- constructor: Function components don’t need a constructor. You can initialize the state in the **useState** call.
- getDerivedStateFromProps: [You probably won’t need it](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html). But you can Schedule an update while rendering instead.
- shouldComponentUpdate: See **React.memo**.
- render: This is the **function component body** itself.
- componentDidMount, componentDidUpdate, componentWillUnmount: **useEffect**,.

React elements are immutable. Once you create an element, you can’t change its children or attributes.

### Rendering

Changes in state and props causes re-renders-

### Ref forwarding

[Ref forwarding](https://reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components) is an opt-in feature that lets some components take a ref they receive, and pass it further down (in other words, “forward” it) to a child.
This allows a component to reach elements in child components.

### Mount and Instancing

React DOM compares the element and its children to the previous one, and only applies the DOM updates necessary to bring the DOM to the desired state.

First render and mount happens, when you initiate an async action, causing the component to re-render. This will also necessitate unmounting the first instance and mounting the second.
While the first instance is unmounted, it still exists in memory and is waiting on the async operation to complete.

When that happens, setLoading(false) and setData(..) are called, these operations are batched and will only cause a single render. When that render happens, the second instance will be unmounted and replaced with the third instance.

So why don't we get "Unmounted" errors?

This is because how hooks are designed, each instance of a specific component (and the exact meaning of 'specific' can be a bit murky) shares the same copy of the state setting function, and as long as one instance is mounted, the call will succeed.

Eventually you'll find yourself with a single instance, but the while an async operation is pending, you will have multiple instances. When the async function exists, the old instance will be disregarded.

If you were to put that component in a modal, trigger the load and then close the modal, you would get the warning of trying to set state on an unmounted component, as when the modal closes it will remove all mounted instances of the component

### Error Boundries

[Error boundaries](https://reactjs.org/docs/error-boundaries.html) are React components that catch JavaScript errors anywhere in their child component tree, log those errors, and display a fallback UI instead of the component tree that crashed.

- Only class components can be error boundaries.
- Error boundaries do not catch errors inside event handlers.

Why not try/catch?

- Only works for imperative code. React components are declarative and specify what should be rendered

### Ucontrolled Components

An uncontrolled component keeps the source of truth in the DOM. [Article](https://goshacmd.com/controlled-vs-uncontrolled-inputs-react/). In React, an `<input type="file" />` is always an uncontrolled component because its value can only be set by a user, and not programmatically.

## Hooks

- Best practice [cheat sheet](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/).
- [React Reference Guide: Hooks API](https://blog.logrocket.com/react-reference-guide-hooks-api/).

## useState:

- Preserve state between re-renders.
- Calls to setState are asynchronous, [React diesb't update state until next re-render](https://reactjs.org/docs/faq-state.html#why-is-setstate-giving-me-the-wrong-value).
- setState is stable and won’t change on re-render. This iw why it is safe to exclude from useEffect/useCallback dependency list.
- you can merge update objects with objective spread syntax.
- “expensive” initial states, provide a function instead of a value and it will only be executed on initial render.

[Warning: Can't call setState (or forceUpdate) on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in the componentWillUnmount method.](https://www.robinwieruch.de/react-warning-cant-call-setstate-on-an-unmounted-component/)

### useReducer

- Use useState whenever you manage a JS primitive
- Use useReducer whenever you manage an object or array
- When you have complex state logic that involves multiple sub-values or when the next state depends on the previous one.
- Want a conditional in your state update? here u go.
- Can have initial state or a Lazy initialization.
- When you spot multiple setState() calls in succession, try to encapsulate these changes in a reducer function that dispatches a single action.

[How to use useReducer](https://adamrackis.dev/state-and-use-reducer/)

## useEffect

When you call useEffect, you’re telling React to run your “Effect” Function after [flushing](flushing) changes to the DOM.
By default, React runs the effects after every render, including the first render.

[Article](https://blog.logrocket.com/guide-to-react-useeffect-hook/).

Functions defined in the body of your function component get recreated on every render cycle. Always use for async tasks.

- Runs after browser has [painted](#browser-painting).
- Doesn't block UI.
- Tells React that your component needs to do something after render.
- The Effect Function is different on every render. Every render creates a different effect. ???
- The array contains the changes that triggers useEffect. You must use all values from the component scope that
  - changes over time
  - are used by useEffect to avoid stale reference value
- The props and state will only have initial values.

**what happens before mount? why. would we wait? What can we do directly in the root component?**

### If you can't move a function inside the useEffect:

1. You can try moving that function outside of your component. Then it's guaranteed not to referense props or state.
2. If the function is a pure computation and is safe to call while renderinf, call it outside the useEffect, and make useEffect dependaant on returned value.
3. As a last resort, you can add a function to effect dependencies but wrap its definition into the useCallback Hook. As a last resort, you can add a function to effect dependencies but wrap its definition into the useCallback Hook.

### If a dependency changes too often

- Don't reference the state value, reference the setValue. It's garanteed to be stable.
- In more complex cases (such as if one state depends on another state), try moving the state update logic outside the effect with [the useReducer Hook](https://adamrackis.dev/state-and-use-reducer/).
- As a last resort, use a ref to hold a mutable value. THis is risky as it might make components less prefictable.

### Async and Promises

A useEffect hook should return nothing or a cleanup function. This is why it can't be async, as async functions always returns a promise.Instead use the async function inside the effect.

### Cleanup

Cleanup functions are not only invoked before destroying the React component. An effect’s cleanup function gets invoked every time, right before the execution of the next scheduled effect.

- Remove event listeners, subscriptions, intervals...

### Dangers!

You cant access current state inside useEffect: [UseEffect And Stale Closures](https://medium.com/edonec/useeffect-and-stale-closures-ea74df7ac452), [Hooks, Dependencies and Stale Closures](https://tkdodo.eu/blog/hooks-dependencies-and-stale-closures)

### useLayoutEffect

For DOM mutations that are visible to the user. The signature is identical to useEffect, but it fires synchronously after all DOM mutations. Use this to read layout from the DOM and synchronously re-render. Updates scheduled inside useLayoutEffect will be flushed, [^flushing] synchronously, before the browser has a chance to paint.
In simplified terms, useLayoutEffect doesn’t really care whether the browser has painted the DOM changes or not. It triggers the function right after the DOM mutations are computed.

#### Why do we care?

You have to be careful not to block the user from seeing visual updates just because you’re running a heavy computation in the background.

PREVENT FLICKER!!

A DOM mutation that must be visible to the user should be fired synchronously before the next paint, preventing the user from receiving a visual inconsistency. We get a FASTER visual effect! [Source 1](https://blog.logrocket.com/useeffect-vs-uselayouteffect-examples/).

## useCallback

Pass an inline callback and an array of dependencies. useCallback will return a memoized version of the callback that only changes if one of the dependencies has changed. This is useful when passing callbacks to optimized child components that rely on reference equality to prevent unnecessary renders

- Returns a memorised callback. a cached result.
- will only change when a dependancy changes.
- Will still be recreated on every render.
- Is useful when passing callbacks to Optimised Child Components that needs Reference Equality to prevent unnecessary renders.

## useMemo

The function passed to useMemo runs during rendering. Don’t do anything there that you wouldn’t normally do while rendering.
If no array is provided, a new value will be computed on every render. For rare cases when a value must never be recomputed, you can lazily initialize a ref.)

**You may rely on useMemo as a performance optimization, not as a semantic guarantee**. In the future, React may choose to “forget” some previously memoized values and recalculate them on next render, e.g. to free memory for offscreen components. Write your code so that it still works without useMemo — and then add it to optimize performance.

- returns a memorized/cached value.
- Will only recompute the value when one of the dependencies has changed.
- The function passed to useMemo runs during rendering
- WRITE CODE THAT WORKS WITHOUT MEMO, do not start with useMemo,
- what makes something expensive enough for useMemo?

useMemo lets you memoize an expensive calculation if the dependencies are the same. However, it only serves as a hint, and doesn’t guarantee the computation won’t re-run. But sometimes you need to be sure an object is only created once.

## useReducer

Lets you manage local state of complex components with a reducer:

## useRef

Returns a mutable object with .current set to “initialValue”.
Will give you the same object every,
Mutating .current does not cause a re-render.

Essentially, useRef is like a “box” that can hold a mutable value in its .current property.
You might be familiar with refs primarily as a way to access the DOM. If you pass a ref object to React with <div ref={myRef} />, React will set its .current property to the corresponding DOM node whenever that node changes.
It’s handy for keeping any mutable value around similar to how you’d use instance fields in classes.

Unless you’re doing lazy initialization, avoid setting refs during rendering — this can lead to surprising behavior. ????

## useContext

Context is primarily used when some data needs to be accessible by many components at different nesting levels

Accepts a context object (the value returned from React.createContext) and returns the current context value for that context. The current context value is determined by the value prop of the nearest <MyContext.Provider> above the calling component in the tree.
When the nearest <MyContext.Provider> above the component updates, this Hook will trigger a rerender with the latest context value passed to that MyContext provider. Even if an ancestor uses React.memo, a rerender will still happen starting at the component itself using useContext.

A component calling useContext will always re-render when the context value changes. If re-rendering the component is expensive, you can optimize it by using memoization.

Optimization

1. (Preferred): Split contexts that don't change together.
2. Split your component in two, put memo in between. The outer still re-renders but the expensive inner one wont.
3. One component with useMemo inside. Keeping it in a single component by wrapping return value in useMemo and specifying its dependencies. Our component would still re-execute, but React wouldn't re-render the child tree if all useMemo inputs are the same.

### useImperativeHandle

customizes the instance value that is exposed to parent components when using ref. Imperative code using refs should be avoided in most cases. useImperativeHandle should be used with forwardRef:

### useDeferredValue

Accepts a value and returns a new copy of the value that will defer to more urgent updates. If you want to prevent a child component from re-rendering during an urgent update, you must also memoize that component with React.memo or React.useMemo:

### useTransition

Returns a stateful value for the pending state of the transition, and a function to start it.

### useId

useId is a hook for [generating unique IDs](https://reactjs.org/docs/hooks-reference.html#useid) that are stable across the server and client, while avoiding hydration mismatches.

## Custom Hooks

A way to reuse stateful logic, not state itself. Each call to a Hook has a completely isolated state — so you can use the same custom Hook multiple times in one component.

You can pass information between hooks:

```javascript
const [recipientID, setRecipientID] = useState(1);
const isRecipientOnline = useFriendStatus(recipientID);
```

## ADITIONAL NOTES

### Arrow functions in render

Can cause performance issues.

### Create Expensive Object Lazily

Will run on every render :(

```javascript
function Table(props) {
  // ⚠️ createRows() is called on every render
  const [rows, setRows] = useState(createRows(props.count));
}
```

To avoid re-creating the ignored initial state, we can pass a function to useState, and the function will only be called the first render.

```javascript
function Table(props) {
  const [rows, setRows] = useState(() => createRows(props.count));
}
```

You might also occasionally want to avoid re-creating the useRef() initial value. For example, maybe you want to ensure some imperative class instance only gets created once:

```javascript
function Image(props) {
  // ⚠️ IntersectionObserver is created on every render
  const ref = useRef(new IntersectionObserver(onIntersect));
}
```

useeRef does not accept a special function overload like useState. Instead, you can write your own function that creates and sets it lazily:

```javascript
function Image(props) {
  const ref = useRef(null);

  // IntersectionObserver is created lazily once
  function getObserver() {
    if (ref.current === null) {
      ref.current = new IntersectionObserver(onIntersect);
    }
    return ref.current;
  }
```

- [How to avoid passing callbacks down?](https://reactjs.org/docs/hooks-faq.html#how-to-avoid-passing-callbacks-down)'
- [How to read an often-changing value from useCallback?](https://reactjs.org/docs/hooks-faq.html#how-to-read-an-often-changing-value-from-usecallback)
- [Pevent a function from being called too quickly or too many times in a row?](https://reactjs.org/docs/faq-functions.html#how-can-i-prevent-a-function-from-being-called-too-quickly-or-too-many-times-in-a-row)

React 18 in Next.Js? Still experimental. We are in 17 so far.
we have react 12.1 since 3 months (march 22)

there are different components in memory, so multiple state?

## React functions

### React.memo HOC

shallowly compare its props.

- If a component renders the same result with the same props.
- DO NOT use to prevent a render.

- createElement: when not using JSX. why do we have it in code? When merging props for a new element?
- cloneElement: why is this used? When overriding props?
- isValidElement()
- React.Children provides utilities for dealing with the this.props.children opaque data structure.

### React.createRef

Creates a ref that can be attached to elements or class components. Ref attribute does not work on function components, use forwardRef there.

### React.forwardRef

Creates a React component that [forwards the ref attribute](https://reactjs.org/docs/forwarding-refs.html) it receives to another component below in the tree.

### React.lazy

React.lazy() lets you define a component that is loaded dynamically. This helps reduce the bundle size to delay loading components that aren’t used during the initial render.

### React.Suspense

Lets you specify the loading indicator in case some components in the tree below it are not yet ready to render.

## ReactDOM

The react-dom package provides DOM-specific methods that can be used at the top level of your app and as an escape hatch to get outside the React model if you need to.

- [Portals](https://reactjs.org/docs/portals.html): you need the child to visually “break out” of its container. For example, dialogs, hovercards, and tooltips. An event fired from inside a portal will propagate to ancestors in the containing React tree, even if those elements are not ancestors in the DOM tree.

- flushSync(): Force React to flush any updates inside the provided callback synchronously. This ensures that the DOM is updated immediately.

- render(): Render a React element into the DOM in the supplied container and return a reference to the component
- -hydrate(): Is used to hydrate a container whose HTML contents were rendered by ReactDOMServer. React will attempt to attach event listeners to the existing markup.

## ReactDOMClient

The react-dom/client package provides client-specific methods used for initializing an app on the client. Most of your components should not need to use this module.

## ReactDOMServer

The ReactDOMServer object enables you to render components to static markup.

- renderToString(element): Render a React element to its initial HTML. React will return an HTML string.

### Browser Painting

1. Browser creates the DOM and CSSOM.
2. Browser creates the render tree, where the DOM and styles from the CSSOM are taken into account (display: none elements are avoided).
3. Browser computes the geometry of the layout and its elements based on the render tree.
4. Browser **paints** pixel by pixel to create the visual representation we see on the screen.

[^flushing]: Flushing forces complete re-rendering for updates that happen inside of a call.

```typescript
useEffect(() => {
  let didCancel = false;

  const fetchData = async () => {
    dispatch({ type: "FETCH_INIT" });

    try {
      const result = await axios(url);

      if (!didCancel) {
        dispatch({ type: "FETCH_SUCCESS", payload: result.data });
      }
    } catch (error) {
      if (!didCancel) {
        dispatch({ type: "FETCH_FAILURE" });
      }
    }
  };

  fetchData();

  return () => {
    didCancel = true;
  };
}, [url]);
```

```typescript
const [state, dispatch] = useReducer(dataFetchReducer, {
  isLoading: false,
  isError: false,
  data: initialData,
});

const dataFetchReducer = (state, action) => {
  switch (action.type) {
    case "FETCH_INIT":
      return {
        ...state,
        isLoading: true,
        isError: false,
      };
    case "FETCH_SUCCESS":
      return {
        ...state,
        isLoading: false,
        isError: false,
        data: action.payload,
      };
    case "FETCH_FAILURE":
      return {
        ...state,
        isLoading: false,
        isError: true,
      };
    default:
      throw new Error();
  }
};
```
