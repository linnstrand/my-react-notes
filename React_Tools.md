# React Resources.

## Build System

### Nx

- Integrate new tooling (Storybook, Tailwind, etc).
- Scaffold new projects.
- Ensure consistency and code quality ith generators and lint rules.

## Builders

### Vite

[Why Vite?](https://vitejs.dev/guide/why.html) Uses Rollup/[ESBuild](https://esbuild.github.io/). Rollup is used for production, while esbuild is used for dev. Comes with a fast Hot Module Replacement.

Unlike a CRA, Vite does not build the entire application before serving. It divides the application modules into two categories: dependencies and source code.
Vite pre-bundles dependencies that does not change often, like mui.

Webpack Bundles the entire app before it an be served. As the project grows, changes will take more and more time.

Vite is used by Cypress, Storybook, Tailwind, Astro.

## Tools

### Bundlers

Bundlers are tools that take multiple source files (often written in different languages or formats) and combine them into a single, optimized output file or a set of files. Webpack, Parcel and Rollup does this.

Webpack, Parcel, Rollup, ESBuild.

### Compilers

Translates source code from one language to another.
Babel, SWC, used by Next.js and Parcel, and are 17-70x faster then babel, depending on number or cores. EsBuild.

### Transpilers

Concerts source code from one version of a language to another without changing it's functionality. Used to write code with the latest language features while ensuring backwards compatibility.

Babel, SWC.

### Linting

SonerQube?

[Eslint with confidence.](https://z1.digital/blog/eslint-guide-how-to-use-it-with-confidence)
eslint should analyze code, not format. Disable all formatting rules in your ESLint preset using `eslint-config-prettier` so that ESLint is only used for catching logical mistakes.
For React: `eslint-plugin-react-hooks`. CRA comes with `eslint-config-react-app`.

Included in Next.js: [ESLint plugin](https://nextjs.org/docs/pages/building-your-application/configuring/eslint#eslint-plugin).
`eslint-plugin-jsx-a11y` to help catch accessibility issues

#### MUI Eslint

Using [AirBnB Style](https://github.com/airbnb/javascript), see the MUI
[eslintrc](https://github.com/mui/material-ui/blob/master/.eslintrc.js) file.

#### Some rules

- eslint-config-airbnb-base/rules/style
- plugin:eslint-plugin-import/recommended
- plugin:eslint-plugin-import/typescript
- eslint-config-airbnb
- eslint-config-airbnb-typescript
- eslint-config-prettier

parser: '@typescript-eslint/parser

plugins

- eslint-plugin-material-ui
- eslint-plugin-react-hooks
- @typescript-eslint/eslint-plugin"
- eslint-plugin-filenames

Standards: AirBnB

### Formatting

Prettier: should format code, but don't bother with the rules.
[Eslint Stylistic](https://eslint.style/guide/why) is an alternative.

### Repository Quality

- Husky: For feature branches, I like being able to commit flawed code. Its better to have lint, build and test tools that looks at the code quality.
- lint-staged: Could be alternative to husky. But I prefer it as part of build/test to not break workflows.
- Use `prettier --check` for your continuous integration.
- install esbenp.prettier-vscode, and use format on save.

## Web Workers

Performs background tasks in separate threads.

- **Complex Calculations:** Web workers are ideal for performing complex mathematical calculations or data processing tasks without impacting the main thread.
- **Large Data Handling:** When dealing with large datasets or files, web workers can help process and parse the data in the background.
- **Background Services:** Web workers can be used to run background services such as periodic data synchronization or background notifications.
  [Using Web Workers in React](https://www.dhiwise.com/post/power-of-web-workers-in-react-for-improved-user-experience),
  [Web Workers API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API).

## Web Services

To build progressive web apps. [Article](https://dev.to/llleeeaaannn/vanilla-service-workers-in-react-138).

- **Background sync**
- **Push notifications**
- **Caching strategies for offline support**

## Storybook

development environment for UI components. It allows you to browse a component library, view the different states of each component, and interactively develop and test components. [Video](https://www.youtube.com/watch?v=gdlTFPebzAU).
