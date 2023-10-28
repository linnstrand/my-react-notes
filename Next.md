# Next.js 13+

App or Pages

## app Router

- Built on React Server Components
- Works alongside pages for incremental adaption. App router have priority over Pages.
- App components are React Server Components by default. Client Components are opt in.

## Structuring

[Next.js folder structure](https://nextjs.org/docs/getting-started/project-structure)

## File Conventions

Files with specific behavior and hierarchy.
Colocation: Only files with page or route name are routes.

- layout: shared UI that doesn't re-render on routing. Can't share data with children.
- template: New instance created for each child on navigation. State not preserved.
- error: error boundary. Automatically wraps a route segment and its nested children in React Error Boundary.
- loading: React Suspense boundary. Instant routing.
- not-found: error boundary,
- page: Unique UI of a route and make routes publicly accessible
- default

### Route handlers (req, res)

- Replaces api routes. Use from client side.
- Custom request handler for route.
- route.ts cannot share route segment with page.
- CRUD methods.
- Cached by default.
- Can read cookies and headers.
- middleware.ts defines in root.

## Nested Routes Folder names

- [folder]: dynamic route segment. generateStaticParams for smart data retrieval. Content fetched with generateStaticParams gets memorized when fetch requests with the same arguments are used.
- [...folder], [[...folder]] Catch all/ optional catch all
- (folder): grouping. Organize routes without affecting url. Group routes, e.g section, intent or team.
- [_folder]: private folder outside routing. Helpful for organizing internal files per route. Separates ui logic from routing logic. Sorting and grouping files in code editors.
- Parallel: Show two of more pages at the same time. Useful for dashboards, modals. Created by defining slots @folder.
- Intercepted: load a route from another part of the app within current layout. use with parallel routes for modals.

### \<Link> Component

- Extended \<a>.
- Prefetching: When visible in user's viewport. Special considerations for dynamic routes. Only in production.
- client-side navigation.
- usePathName for active status
- useRouter for programmatic routing.

#### Streaming with Suspense

Breaks down the page's HTML in smaller chunks and streams them. Works by wrapping a component that performs an async action with \<Suspense>.

#### Partial Rendering

Only the route segments that change on navigation is re-rendered. Nested layouts are preserved.

## Data Fetching

### Server with `fetch`.

- Next.js `fetch` extends Web API.
- Caching in Data Cache on sever.
- Revalidation: purging Data Cache and re-fetching data.
- Memorize
- Server with third-party libs might not support fetch.

### Client with Route Handler.

- No memorization
- Client with third-party libs

### Patterns and best practices

- When possible, fetch data on the server.
- Fetch data when needed. Data does not have to be fetched globally. Fetch or React cache memoize responses.
- Stream, Parallel, Sequential, Preloading when appropriate.
- Using React cache, server-only, and the Preload Pattern. Combine the cache function, the preload pattern, and the server-only package to create a data fetching utility that can be used throughout your app

### Forms and Mutations

#### Server Actions

- Defined in Server Components or called from Client Components.
- Allows form to function without JavaScript.
- Experimental feature enabled in next-config.js.

## Rendering

React Server Component Payload: Compact binary representation of the rendered React Server Component tree. The rendered result of Server Components.
Placeholder for where Client Components should be rendered. Any Props passed from SC to CC.

### Server Components

- Rendered via Static, Dynamic and Streaming
- Data fetching closer to data source can improve performance.
- Sensitive data and logic on server.
- Results can be cached and reused on server.
- Smaller bundle sizes
- Initial Page Load and First Contentful Paint. No need to wait for necessary JS to execute clientside.
- SEO and SNS (Social Network Shareability).
- Does not support CSS-in-JS libraries that require runtime.

#### Tips

- Use Cache or Fetch instead of Context. see memoization. Testing?
- Props passed to CC needs to be serializable.
- Server Components can be passed as Props to Client Components.

### Client Components

- Interactivity and access to geolocation and localstorage.
- Static HTML preview rendered immediately. Can cause issues with CSS-in-JS.
- Hydrating of components
- Render Context and Provider inside 'use client' components.

#### Tips

- Prevent server only code with sever-only package.
- Sever Components can't be imported into Client Components.

#### Runtimes

- Node (default) :Not as fast, but have all node functions and npm packages.
- Edge is based on Web APIs: For dynamic,personalized content with small, simple functions. Limited.
- Serverless: For more complex computational loads then Edge. Needs "boot up".

## Caching

Fetch cache option: In browser how the requests will interact with browsers HTTP cache. In Next.js how request will interact with servers Data Cache.

### Request Memoization (rendering)

- Caches Function Returns
- Reduces number of _duplicate_ requests in the same render pass.
- React feature, extension of fetch API.
- Server Side.
- Only GET
- Lasts until React Component Tree has finished rendering.
- Call fetch for same URL and Options returns same data in multiple places in React Component Tree.

### Data Cache (persistent)

- JSON
- Reduce number of requests to origin data source.
- Persists the result across server requests and deployments.
- Opt out with 'no-store'
- Revalidating: Time based or on-demand.

### Full Route Cache (RSC Payload + HTML)

- The rendered result (RSC Payload and HTML) is cached by default on server.
- Static routes are cached on default, dynamic routes are rendered at request time.
- Cleared on deployments (unlike data cache).

### Router Cache (RSC Payload)

∑

- Client-side Cache/Prefetch Cache.
- RSC Payload is Stored in client-side Router Cache, Split by Route Segments.
- Prefetches routes user is likely to navigate to based on \<Link> components in the viewport.
- Persist during a Session.
- Automatic Invalidation Period depends on if the route is statically or dynamically rendered.
- Invalidate with router.refresh or in server actions.

## Optimizations

- Components: Images, Link, Scripts.
- Metadata API: For Search Engines and SEO.
- Files inside /public can be cached by CDN.
- next/font will optimize fonts.
- next/script for third party scripts.
- Lazy Loading with next/dynamic (composite of React.Lazy and Suspense). SSR unless disabled.

## Configuring

- Typescript: Keep up to date with new or experimental features.
- ESLint: "lint" script enabled by default.
- Prettier: Can conflict with ESLint. use `eslint-config-prettier` in your ESLint config to make ESLint and Prettier work together.
  Add prettier to eslintrc.json in extends array.
- `lint-staged` runs linter on staged files.
- For config migrations see docs.
- Module aliases can be set in tsconfig.json compilerOptions.
- MDX: with @next/mdx
- Draft Mode to render pages on request time, for working with headless CMS drafts.
- Content Security Policy (CSP): a Nonce is used in conjunction with CSP to selectively allow certain inline scripts/Styles.

## Architecture

- Fast Refresh: Fie most only export React component(s)
- Compiler: In rust using SWC. 17x faster then Babel since v12 and default.
- Turbopack is in beta.

SWC
