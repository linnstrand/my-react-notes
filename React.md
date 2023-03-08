# What is React?

- JavaScript library for rendering user interfaces (UI).
- Combines small unites into reusable, nestable components.
- A component is a function that contain HTML-like markup written with JSX/TSX.
- JS syntax can be used for conditional rendering.
- Providing a fast user experience by only updating the parts of the UI that have changed.
- VDOM: abstraction layer on top of the DOM.
- React DOM compares the element and its children to the Browser DOM, and only updates the elements that's different.
- React elements are immutable. Once you create an element, you can’t change its children or attributes.

### Pure functions

- Does not change anything outside itself.
- Same input, same output.

# React Lifecycle and Hooks

React has four built-in methods that gets called, in this order, when mounting a component. These are outdated with function components. Hooks lets us split a component based on related pieces rather then split on lifecycle methods.

1. Mounting (adding nodes to the DOM)
2. Updating (altering existing nodes in the DOM)
3. Unmounting (removing nodes from the DOM)
4. Error handling (verifying that your code works and is bug-free)

### Rendering Elements, Mount and Instancing

Changing state causes re-rendering of the component and its children.
This is the reason changes in prop causes re-renders.
When a component re-render, it unmounts the first instance and mounts the second.
While the first instance is unmounted, it still exists in memory and is waiting on the async operation to complete.

#### Controlling Rerenders

- React.memo: Prevents React hook components from rendering when the props doesn't change.
- Avoid placing logic in the root. Place the logic at a "bottom", close to where the data is used:

#### Unmounting errors

While an async operation is pending, you will have multiple instances. When the async function exists, the old instance will be disregarded.

If you were to put that component in a modal, trigger the load and then close the modal, you would get the warning of trying to set state on an unmounted component, as when the modal closes it will remove all mounted instances of the component

##### So why don't we get "Unmounted" errors?

This is because how hooks are designed, each instance of a specific component shares the same copy of the state setting function, and as long as one instance is mounted, the call will succeed.

[More on Rendering](https://felixgerschau.com/react-rerender-components/#what-is-rendering).

### Error Boundaries

React components that catch JavaScript errors anywhere in their child component tree, log those errors, and display a fallback UI instead of the component tree that crashed.

- Only class components can be error boundaries.
- Error boundaries do not catch errors inside event handlers.

Why not try/catch?

- Only works for imperative code. React components are declarative and specify what should be rendered.

### Uncontrolled Components

##### I’m getting an error: “A component is changing an uncontrolled input to be controlled”

If you pass value={undefined} and later change it to string, react wont know if the component should ve controlled or uncontrolled. An controlled component should always receive a string value, not null or undefined. Make sure to default to empty string.

An uncontrolled component keeps the source of truth in the DOM. [Article](https://goshacmd.com/controlled-vs-uncontrolled-inputs-react/). In React, an `<input type="file" />` is always an uncontrolled component because its value can only be set by a user, and not programmatically.

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
}
```

- [How to avoid passing callbacks down?](https://reactjs.org/docs/hooks-faq.html#how-to-avoid-passing-callbacks-down)'
- [How to read an often-changing value from useCallback?](https://reactjs.org/docs/hooks-faq.html#how-to-read-an-often-changing-value-from-usecallback)
- [Prevent a function from being called too quickly or too many times in a row?](https://reactjs.org/docs/faq-functions.html#how-can-i-prevent-a-function-from-being-called-too-quickly-or-too-many-times-in-a-row)

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

## Refs

- When a component should remember some information, but the information shouldn't trigger new render, use ref.
- When information is only needed for event handlers, a ref might be more efficient then useState.
- Storing and manipulating DOM elements.

[Ref forwarding](https://reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components) is an opt-in feature that lets some components take a ref they receive, and pass it further down (in other words, “forward” it) to a child.
This allows a component to reach elements in child components.

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
    dispatch({ type: 'FETCH_INIT' });

    try {
      const result = await axios(url);

      if (!didCancel) {
        dispatch({ type: 'FETCH_SUCCESS', payload: result.data });
      }
    } catch (error) {
      if (!didCancel) {
        dispatch({ type: 'FETCH_FAILURE' });
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
    case 'FETCH_INIT':
      return {
        ...state,
        isLoading: true,
        isError: false,
      };
    case 'FETCH_SUCCESS':
      return {
        ...state,
        isLoading: false,
        isError: false,
        data: action.payload,
      };
    case 'FETCH_FAILURE':
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
