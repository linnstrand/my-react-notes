# React Code Standards

## Naming Conventions

- Prefer to use named exports
- [Avoid default exports](https://basarat.gitbook.io/typescript/main-1/defaultisbad): Default exports are good when you want whatever a file exports. When you want to use any name. Avoiding default exports promotes consistent name. Default exports can lead to stale references. Eslint rules can enforce this.
- Forward props with spread syntax `<Avatar {...props} />`.
- Careful when using `&&`. React renders 0 and NaN. Solutions is being more explicit `array.length > 0` or `!!array.length` or ternary operator.
- [Barrel file](https://basarat.gitbook.io/typescript/main-1/barrel): `index.ts` is useful for exporting multiple components from a path.

### Splitting Components

- Programming: single responsibility principle: a component should only do one thing.
- Readability: It should be clear which state and which event handler belongs to what part of the JSX.
- Testing: Smaller components makes testing easier.
- Code Review: Smaller files makes for clearer differences and reduces merge conflicts.
- DRY, but KISS. Sometimes repeated code is better then complex abstraction.

## Performance

- The Profiler react component can be used to messure.
- the Profiler tab in React Developer Tools exposes similar functionality as the Profiler component.

### React.memo

- React.memo: Prevents React hook components from rendering when the props doesn't change.
- Optimizing with memo is only valuable when your component re-renders often with the same exact props, and its re-rendering logic is expensive.
- A passed object/function is always different. Use useMemo and useCallback with memo.

## Optimization

- Split contexts that don't change together.
- Split your component in two, put memo in between. The outer still re-renders but the expensive inner one won't.
- Avoid unnecessary useEffets that updates state.

## Data Fetching

useEffect delays data fetching until app is rendered. This slows the app by requiring download and render off app before data fetc can be started. There is also a risk of "network waterfalls". useEffects means data isn't preloaded or cached, and retrived on every mount/umount. Risk for race conditions (responses arrives in unexpected order).

Instead, try to use frameworks built in data fetching mechanisms. Libraries like can also do client side cache, see React Query, React Router, useSWR.

## Unmounting errors

While an async operation is pending, you will have multiple instances. When the async function exists, the old instance will be disregarded.

If you were to put that component in a modal, trigger the load and then close the modal, you would get the warning of trying to set state on an unmounted component, as when the modal closes it will remove all mounted instances of the component

##### So why don't we get "Unmounted" errors?

This is because how hooks are designed, each instance of a specific component shares the same copy of the state setting function, and as long as one instance is mounted, the call will succeed.

[More on Rendering](https://felixgerschau.com/react-rerender-components/#what-is-rendering).

## Linting

- https://z1.digital/blog/eslint-guide-how-to-use-it-with-confidence

## Formatting

## Libraries and Extensions

## [TanStack](https://tanstack.com/)

- Released: Query, Table, Store.
- Beta: Router, Ranger
- Alpha: Loaders, Actions
  s
