# React State Management

## React Hooks

- UseEffect should not be used for changing state, as it's a side effect. State should change according to events. Either user triggered, or from a external source, like an api or library.
- Be aware of react rendering rules: [A Visual Guide to React Rendering](https://alexsidorenko.com/blog/react-render-cheat-sheet/), [A (Mostly) Complete Guide to React Rendering Behavior](https://blog.isquaredsoftware.com/2020/05/blogged-answers-a-mostly-complete-guide-to-react-rendering-behavior/), [Re-Render Guide](https://www.developerway.com/posts/react-re-renders-guide).

#### useSyncExternalStore

Lets you subscribe to external store.

- Parameters: subscribe: When store changes, the subscribe callback function executes and re-render the component. getSnapshot: Returns same value until store changes. getServerSnapshot: a function that returns the initial snapshop of the data in the store. Used only during server rendering and hydration of SSC on client.

## Context

Context is great for sharing small states, that doesn't change much, as any change to it will cause all descendants to re-render. Track if it's a problem with react devtools Profiler.

## Zustand

- Client side.
- Not based on Context. Replacement for Context.

## TanStack React Query

- Data from API endpoint.
- Fetch, store, refetch and use user data from api in multiple components.

## Jotai

- Small atoms of state that are needed in multiple components.
- Works like global useState.

## React Hook Form

## Routing

- react-router has abandoned usePrompt and useBlocker.
- TanStack Router has Navigation Blocking. Its in beta atm, but promising.
