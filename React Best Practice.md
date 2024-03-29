# React Code Standards

### Goals

- Quality and Consistency.
- Readability
- Security
- Maintainability and Scaling.
- Clarity and Onboarding.

## Naming Conventions

- Prefer to use named exports
- [Avoid default exports](https://basarat.gitbook.io/typescript/main-1/defaultisbad): Default exports are good when you want whatever a file exports. When you want to use any name. Avoiding default exports promotes consistent name. Default exports can lead to stale references. Eslint rules can enforce this.
- Forward props with spread syntax `<Avatar {...props} />`.
- Careful when using `&&`. React renders 0 and NaN. Solutions is being more explicit `array.length > 0` or `!!array.length` or ternary operator.
- [Barrel file](https://basarat.gitbook.io/typescript/main-1/barrel): `index.ts` is useful for exporting multiple components from a path.
- [Naming Cheatsheet](https://github.com/kettanaito/naming-cheatsheet)

### Splitting Components

- Programming: single responsibility principle: a component should only do one thing.
- Readability: It should be clear which state and which event handler belongs to what part of the JSX.
- Testing: Smaller components makes testing easier.
- Code Review: Smaller files makes for clearer differences and reduces merge conflicts.
- DRY, but KISS. Sometimes repeated code is better then complex abstraction.

### Small component trees.

- React needs to diff the entire tree every time state changes.

## Performance

1. **React DevTools:**

   - Use "Components" tab to inspect the component hierarchy and track component updates.
   - Use the "Profiler" tab to analyze the performance of specific parts of your application and identify components that re-render frequently.

2. **Google Lighthouse**

   - Lighthouse provides audits for performance, accessibility, best practice, etc.
   - Receive a comprehensive report on various aspects of your web application's performance.

3. **Performance Timeline**

   - Record a performance profile and analyze the timeline to identify rendering bottlenecks and excessive re-renders.
   - Visualize the timeline of various events during application execution.
   - Identify tasks that consume significant time during rendering.
   - Inspect layout and paint operations.

4. **Network Activity:**

   - Use to analyze the loading times of resources.
   - Identify potential bottlenecks in data fetching and API requests.
   - Use it to understand the impact of network requests on overall application performance and optimize data fetching strategies.

5. **React Strict Mode:**

   - Detect and log common mistakes such as side effects during rendering.
   - Highlight potential issues with deprecated lifecycle methods.

6. **React Profiler API:**

   - Wrap the code you want to profile in a `Profiler` component to identify components that need optimization.
   - Gain fine-grained insights into component rendering performance.

7. **Performance.now() and Console Logging:**

   - Insert `console.log` statements with `Performance.now()` to measure the time taken by specific code blocks.
   - Measure the time taken by specific operations.

8. **Bundle Analysis Tools:**

   - Use tools like `source-map-explorer`, `vite-bundle-visualizer`, `nx graph` to analyze your application.
   - Integrate with your build process to generate reports.
   - Identify large dependencies contributing to bundle size.
   - Optimize imports and remove unnecessary dependencies.

### Actions

- SSR can speed up initial loads to improve time-to-interactivity.
- Avoid request waterfalls, where a request have to finish before another is started. Can be avoided with pre-fetching.
  - libraries like react-query helps avoid prefetching data.
- Tools like DataDog, Grafana, Prometheus can track performance and infrastructure metrics.
- Establish alerting thresholds for critical performance metrics.
- Use Immer for immutable data structures, which allows us to make changes to out state without creating new arrays or objects.
- Use Web Workers to perform heavy computations.
- Use Service workers for caching.

## Optimization

- Split contexts that don't change together.
- Split your component in two, put memo in between. The outer still re-renders but the expensive inner one won't.
- Avoid unnecessary useEffects that updates state.
- Virtualize long lists, that only renders the visible parts.
- Minimize numbers of API requests using caching and payload size strategies.
- Avoid unnecessary backwards compatibility. This saves us from polyfill hell.

## Linters

1. **Code Quality:** Linters analyze your code for potential errors, bugs, or code smells. They help catch common mistakes and encourage adherence to coding standards.
2. **Consistency:** Linters enforce coding conventions and style guidelines, ensuring that your codebase follows a consistent and readable format. This is particularly important in collaborative projects with multiple developers.
3. **Early Error Detection:** Linters can catch errors and potential issues early in the development process. This helps in reducing the debugging time and ensures that issues are addressed before they become more complex.
4. **Security:** Some linters can detect security vulnerabilities or potential security risks in your code. This is crucial for writing secure software and protecting against common security pitfalls.
5. **Best Practices:** Linters often incorporate best practices and recommendations for coding style and structure. Following these best practices can lead to more maintainable and scalable code.
6. **Code Reviews:** Linters can be used as part of the code review process, making it easier for developers to spot and address issues. This promotes a collaborative and constructive code review environment.
7. **Customization:** Linters are often configurable, allowing you to tailor the rules to fit the specific requirements of your project or team.

## Formatters

1. **Consistent Code Style:** Formatters automatically enforce a consistent code style throughout your project. This is essential for readability and reduces the likelihood of debates over code formatting during code reviews.
2. **Time Savings:** Manual code formatting can be time-consuming and error-prone. Formatters automate this process, saving developers time and ensuring that formatting is consistently applied.
3. **Reduced Bike-shedding:** Code formatting debates can sometimes distract from more critical discussions during code reviews. By using a formatter, you can largely eliminate these discussions and focus on more substantial aspects of the code.
4. **Integration with Version Control:** Formatters can be integrated into version control systems, ensuring that the entire team is using the same formatting rules. This helps in maintaining a clean and consistent codebase.
5. **Ease of Onboarding:** For new team members, using a formatter means they can adhere to the established coding standards without having to spend time manually formatting their code.
6. **Enforced Best Practices:** Formatters often follow best practices for code formatting, helping developers write code that is not just consistent but also adheres to industry standards.

## Merge Conflicts

#### Minimize merge conflicts

- Smaller PR's.
- Smaller Files and functions.
- Independent modules.
- Standardize formatting rules.
- Trailing comma.
