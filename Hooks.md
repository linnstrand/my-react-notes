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

- Returns a memorized callback. a cached result.
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
