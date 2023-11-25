# What is React?

- JavaScript library for rendering UI.
- Combines small unites into reusable, nestable components, functions that contain HTML-like markup written with JSX/TSX.
- Providing a fast user experience by only updating the parts of the UI that have changed.
- VDOM: abstraction layer on top of the DOM.
- React elements are immutable. Once you create an element, you can’t change its children or attributes.

## React Lifecycle

React has four built-in methods that gets called, in this order, when mounting a component. These are outdated with function components. Hooks lets us split a component based on related pieces rather then split on lifecycle methods.

1. Mounting (adding nodes to the DOM)
2. Updating (altering existing nodes in the DOM)
3. Unmounting (removing nodes from the DOM)
4. Error handling (verifying that your code works and is bug-free)

## Components

- Should be Pure Functions. Does not change anything outside itself. Same input, same output, no matter where it's used.
- Avoid default exports: Default exports are good when you want whatever a file exports. When you want to use any name. Avoiding default exports promotes consistent name. Default exports can lead to stale references. Eslint rules can enforce this.
- Forward props with spread syntax.
- Careful when using `&&`. React renders 0 and NaN. Solutions is being more explicit `array.length > 0` or `!!array.length` or ternary operator.
- Controlled component: Driven by props.
- Uncontrolled component: Driven by state (Parent cannot influence the component).

#### key attribute

- Connects component to value.
- Must be unique and not change.
- In array rendering, it matches an array item to component, and makes sure the DOM updates correctly when adding and removing elements.
- Preferdly use database keys/IDs. If generated locally, use a incrementing counter, or UUID.

#### Event handlers

- Event handlers should receive a method, not a executed method. use `() => Method()` if you don't want to pass the whole event to the handler.
- Event propagation. All events except onScroll propagate. Preventable with e.stopPropagation().
- Default behavior: `form` elements submit are triggered by any button inside it by default.

#### Uncontrolled Components

> I’m getting an error: “A component is changing an uncontrolled input to be controlled”

If you pass `value={undefined}`` and later change it to string, react wont know if the component should ve controlled or uncontrolled. An controlled component should always receive a string value, not null or undefined. Make sure to default to empty string.

An uncontrolled component keeps the source of truth in the DOM. [Article](https://goshacmd.com/controlled-vs-uncontrolled-inputs-react/). In React, an `<input type="file" />` is always an uncontrolled component because its value can only be set by a user, and not programmatically.

### Component State

- Persist values between renders (local variables are reset).
- Trigger re-render on change (local variables does not trigger re-render).s
- Place state in the closest common parent component. If there is no suitable component, create a new component for the state.
- Lives outside the component function.
- Only changes for the next render.
- Batch updates. React waits until all code in an event handler have executed before re-rendering.

#### [React documentation on managing state](https://react.dev/learn/managing-state)

- Use states to manipulate UI declaratively. Identify the components visual states, and determine what triggers them. [declarative UI](https://react.dev/learn/reacting-to-input-with-state#how-declarative-ui-compares-to-imperative).
- State Structure: Group related state in single variables. Avoid contradictory states.
- Do not duplicate state. If you have a list of objects, do not duplicate information by using a object for selectedObject. Instead use object id. The selected object can be a local variable found by `items.find()`.
- Avoid nested states. Flatten (normalize) the data.

Avoid redundant states by calculating value during render. Local variables wont trigger re-render.
Calculate the cost, and only memoize if it is above 1ms. Confirm that useMemo actually improves performance. When running this test, throttle CPU.

```ts
console.time("timer name");
const result = getCalculatedValue();
console.timeEnd("timer name");
```

#### Preserve and reset

- When react removes or replaces a component, state is destroyed.
- Same component at the same place preserves the state.
- Different component at the same position resets state.
- Override: Render in different positions OR Reset state at the same position with key attribute.
- Preserve might be best to do by lifting state.

#### Pass Deep

- Prop drilling can make flow and dependencies explicit.
- Typical use-cases: Theming, current user, state that should trigger re-renders.

#### Object Mutations

- All objects should be treated as immutable, even numbers, booleans and strings. Always replace them.
- Copy objects with spread syntax and override properties with new values. This is a shallow copy.
- Nested objects are not really nested, they are references to other object. Mutations need to duplicate multi-level.

```typescript
setPerson({
  ...person, // Copy other fields
  artwork: {
    // but replace the artwork
    ...person.artwork, // with the same one
    city: "New Delhi", // but in New Delhi!
  },
});
```

For deeply nested logic, consider Immer.

#### Array Mutations

- When using array functions, make sure to use a method that returns a new array, and not mutates the current.
- Typescript 5.2 2023 new array methods; `toSorted` returns a new sorted array. `toSpliced` can be used to insert, remove or replace element at specific index. `toReversed` returns a new reversed array. For single elements, `myArray[index] = updatedValue => myArray.with(index, updatedValue)`.
- Remove with `filter` or `toSpliced`.

## Rendering

The calling of the component as a function. Re-rendering does not always mean repainting of the browser DOM.

- Triggers: Initial render, change of state, change of ancestors' state.
  - Changing state causes re-rendering of the component and its children.
    This is the reason changes in prop causes re-renders.
- When a component re-render, it unmounts the first instance and mounts the second.
  - While the first instance is unmounted, it still exists in memory and is waiting on the async operation to complete.
- When rendering is done, the browser will paint.

Controlling Rerenders: React.memo prevents React hook components from rendering when the props doesn't change.
{children} doesn't re-render when wrapping element does. UNLESS you use a memoized component.

### Error Boundaries

Package: react-error-boundary

Class Component: React components that catch JavaScript errors anywhere in their child component tree, log those errors, and display a fallback UI instead of the component tree that crashed.

- Only class components can be error boundaries.
- Error boundaries do not catch errors inside event handlers.
- React's equivalent of try/catch blocks.
- Will react release a Catch component to replace it?

## React API

### React.memo

memo lets you skip re-rendering a component if the props are unchanged.

- shallowly compare its props.
- DO NOT use to prevent a render.
- a memo can use a comparison function when its infeasible to minimize the prop changes.
- Optimizing with memo is only valuable when your component re-renders often with the same exact props, and its re-rendering logic is expensive.
- A passed object/function is always different. Use useMemo and useCallback with memo.

### React.createRef

Creates a ref that can be attached to elements or class components. Ref attribute does not work on function components, use forwardRef there.

### React.forwardRef

- Exposing a DOM node to the parent component.
- Forwards a ref through multiple components.
- useImperativeHandle customizes the handles exposed as a ref. If you don't want to expose the whole DOM node, you can select the properties and methods you need.
- Only use refs for imperative behaviors that you can’t express as props

### React.lazy

React.lazy() lets you defer loading a component-s code until it's rendered for the first time. This helps reduce the bundle size to delay loading components that aren’t used during the initial render.

### React.startTransition

Lets you update the state without blocking the UI. The UI stays responsive in the middle of a re-render.

## React DOM API

### createPortal

Lets yoy render some children into a different part of the DOM. Useful to place a modal in the document body, or outside React.

### React DOM API/Client

- createRoot: replaces render in React 18.
- hydrateRoot: replaces hydrate in React 18.

## React built in components

### \<Profiler>

Lets you measure rendering performance of a React Tree.

### \<StrictMode>

- Helps with finding common bugs during development.
- Re-render an extra time to find bugs caused by unpure rendering.
- Causes Effects to run twice, finding bugs caused by missing Effect cleanup.

### \<Suspense>

Documentation does not make much sense at November 2023. Refers to Next.js Thereafter back to React.

- Only Suspense-enabled data sources will activate the Suspense component.
