# Hooks

- More restrictive then regular functions, Can only be called at tge top of components.
- Best practice [cheat sheet](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/).
- [React Reference Guide: Hooks API](https://blog.logrocket.com/react-reference-guide-hooks-api/).

## useState:

- State works like a snapshot, preserving variables between re-renders.
- Triggers re-render when state changed.
- Should not be changed directly, use spread syntax to copy objects and arrays.
- Calls to setState are asynchronous, React doesn't update state until next re-render, so the value cant be used immediately after change. Batching improves performance and prevents half-finished renders with many state changes.
- setState is stable and won’t change on re-render. This iw why it is safe to exclude from useEffect/useCallback dependency list.
- With “expensive” initial states, provide a function instead of a value and it will only be executed on initial render.
- "Immer" can help making the logic concise.

#### Tips

- Share state by lifting it up to common parent.
- Force state reset by passing it a different key.
- useReducer when things gets complex.
- useContext when passing data deeply.

#### Create Expensive Object Lazily

```javascript
function Table(props) {
  // createRows() is called on every render
  const [rows, setRows] = useState(createRows(props.count));
}
```

To avoid re-creating the ignored initial state, we can pass a function to useState, and the function will only be called the first render.

```javascript
function Table(props) {
  const [rows, setRows] = useState(() => createRows(props.count));
}
```

##### [Warning: Can't call setState (or forceUpdate) on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in the componentWillUnmount method.](https://www.robinwieruch.de/react-warning-cant-call-setstate-on-an-unmounted-component/)

### useReducer

- Use useState whenever you manage a JS primitive
- Use useReducer whenever you manage an object or array
- When you have complex state logic that involves multiple sub-values or when the next state depends on the previous one.
- Want a conditional in your state update? here u go.
- Can have initial state or a Lazy initialization.
- When you spot multiple setState() calls in succession, try to encapsulate these changes in a reducer function that dispatches a single action.

[How to use useReducer](https://adamrackis.dev/state-and-use-reducer/)

## useEffect

By default, React runs the effects after every render, including the first render. Typically used to synchronize component with system outside of React.

- Use when its not a pure calculation or triggered event.
- Code inside useEffect are not run in initial rendering calculation, when there is no DOM. Wrap with useEffect when variables interact with dom (like ref.current).

[Article](https://blog.logrocket.com/guide-to-react-useeffect-hook/).

- Runs after browser has painted.
- Doesn't block UI.
- Runs twice in dev.
- The Effect Function is different on every render. Every render creates a different effect.
- The array contains the changes that triggers useEffect. You must use all values from the component scope that
  - changes over time
  - are used by useEffect to avoid stale reference value
- The props and state will only have initial values.

### Do not use...

- Data Transform: useEffects restarts the whole process of deciding what should be on the screen, commit and render when it updates state. Transform the data on top level.
- User events should use use event handlers, not useEffect.
- For updating state based on props/state. calculate it during render. If calculation is expensive, use useMemo (measure with console.time).
- For resetting states. Instead use a key on the component.
- For adjusting state during render. Do it directly in top layer, with a previousValue useState.
- In chains. use event handler instead. Unless it can't be done in event handler, like forms with dependencies between fields.
- Initializing the app. Instead run functions during module initialization before app renders.
- Subscribing to external store, like a third party library or built-in browser API. Instead use `useSyncExternalStore`
- Fetch data. useEffect delays data fetching until app is rendered. This slows the app by requiring download and render off app before data fetch can be started. There is also a risk of "network waterfalls". useEffects means data isn't preloaded or cached, and retried on every mount/umount. Risk for race conditions (responses arrives in unexpected order). Instead, try to use frameworks built in data fetching mechanisms. Libraries like can also do client side cache, see React Query, React Router, useSWR.

### If you can't move a function inside the useEffect:

1. You can try moving that function outside of your component. Then it's guaranteed not to reference props or state.
2. If the function is a pure computation and is safe to call while rendering, call it outside the useEffect, and make useEffect dependant on returned value.
3. As a last resort, you can add a function to effect dependencies but wrap its definition into the useCallback Hook. As a last resort, you can add a function to effect dependencies but wrap its definition into the useCallback Hook.

### If a dependency changes too often

- Don't reference the state value, reference the setValue. It's guaranteed to be stable.
- In more complex cases (such as if one state depends on another state), try moving the state update logic outside the effect with [the useReducer Hook](https://adamrackis.dev/state-and-use-reducer/).
- As a last resort, use a ref to hold a mutable value. THis is risky as it might make components less predictable.

### Async and Promises

A useEffect hook should return nothing or a cleanup function. This is why it can't be async, as async functions always returns a promise.Instead use the async function inside the effect.

### Cleanup

- Cleanup functions are not invoked every render before the execution of the useEffect code.
- Use cleanup functions if dev mode double render causes issues.
- If a response arrives after cleanup sets a variable to 'ignore', the awaited execution can be ignored.

- Remove event listeners, subscriptions, intervals, animation resets...

### Dangers!

You cant access current state inside useEffect: [UseEffect And Stale Closures](https://medium.com/edonec/useeffect-and-stale-closures-ea74df7ac452), [Hooks, Dependencies and Stale Closures](https://tkdodo.eu/blog/hooks-dependencies-and-stale-closures)

## Unmounting errors

While an async operation is pending, you will have multiple instances. When the async function exists, the old instance will be disregarded.

If you were to put that component in a modal, trigger the load and then close the modal, you would get the warning of trying to set state on an unmounted component, as when the modal closes it will remove all mounted instances of the component

##### So why don't we get "Unmounted" errors?

This is because how hooks are designed, each instance of a specific component shares the same copy of the state setting function, and as long as one instance is mounted, the call will succeed.

[More on Rendering](https://felixgerschau.com/react-rerender-components/#what-is-rendering).

### useLayoutEffect

For DOM mutations that are visible to the user. The signature is identical to useEffect, but it fires synchronously after all DOM mutations. Use this to read layout from the DOM and synchronously re-render. Updates scheduled inside useLayoutEffect will be flushed, synchronously, before the browser has a chance to paint.
In simplified terms, useLayoutEffect doesn’t really care whether the browser has painted the DOM changes or not. It triggers the function right after the DOM mutations are computed.

#### Why do we care?

You have to be careful not to block the user from seeing visual updates just because you’re running a heavy computation in the background.

PREVENT FLICKER!!

A DOM mutation that must be visible to the user should be fired synchronously before the next paint, preventing the user from receiving a visual inconsistency. We get a FASTER visual effect! [Source 1](https://blog.logrocket.com/useeffect-vs-uselayouteffect-examples/).

## useCallback

Pass an inline callback and an array of dependencies. useCallback will return a memoized version of the callback that only changes if one of the dependencies has changed. This is useful when passing callbacks to optimized child components that rely on reference equality to prevent unnecessary renders

- Returns a memorized callback. a cached result.
- Stable Reference.
- will only change when a dependency changes.
- Will still be recreated on every render.
- Is useful when passing callbacks to Optimized Child Components that needs Reference Equality to prevent unnecessary renders.

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

1. Returns a mutable object with .current set to “initialValue”.
2. Works as a _instance variable_, similar to an instance property on a class, lets you keep a mutable object around.
3. Will give you the same object every,
4. Mutating .current does not trigger a re-render.
5. Commonly referring to a DOM node.
6. A function can be used for initial value, .current(prop) executes the method.
7. The reference must be updated either inside a useEffect() callback or inside handlers (event handlers, timer handlers, etc).

[Essentially, useRef is like a “box” that can hold a mutable value in its .current property.](https://reactjs.org/docs/hooks-reference.html#useref)

### Difference to State

- Updating a reference doesn't trigger a re-render.
- Reference update is synchronous, while state is updated after re-rendering. This might be a problem if you want a ref to change after render. If that's the case, useEffect can delay the update until after mounting. [Focusing on input example](https://dmitripavlutin.com/react-useref-guide/#21-use-case-focusing-an-input).

### Refs

- When a component should remember some information, but the information shouldn't trigger new render, use ref.
- When information is only needed for event handlers, a ref might be more efficient then useState.
- Storing and manipulating DOM elements.

[Ref forwarding](https://reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components) is an opt-in feature that lets some components take a ref they receive, and pass it further down (in other words, “forward” it) to a child.
This allows a component to reach elements in child components.

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
}
```

## useContext

Context is primarily used when some data needs to be accessible by many components at different nesting levels

Accepts a context object (the value returned from React.createContext) and returns the current context value for that context. The current context value is determined by the value prop of the nearest <MyContext.Provider> above the calling component in the tree.
When the nearest <MyContext.Provider> above the component updates, this Hook will trigger a rerender with the latest context value passed to that MyContext provider. Even if an ancestor uses React.memo, a rerender will still happen starting at the component itself using useContext.

A component using a Context will always re-render when the context value changes.

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
