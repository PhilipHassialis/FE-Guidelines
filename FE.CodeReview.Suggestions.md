# React Code Guidelines

## Basic Premise

The following are a set of suggestions and guidelines in terms of creating and developing a `React.JS` project both in terms of initial conception and actual coding. They should be considered "good practices" and allow for better maintainability of a codebase. Certain parts of the following are based on intrinsic primitives and fundamentals of the target platform (a user web browser), whilst others focus on what is considered best from the library's vendors perspective.

*Note: the latter parts that focus on libraries and proposals, are suggestinons based on the current state of the ecosystem. Libraries change with the passage of time and with React.JS, Next.JS releasing new versions, they mey need to be revised accordingly.*

## Basic React Project Initiation and Structure

- opt in to use `vite.js` for a non-SSR project creation. Do *avoid* `create-react-app` as it is an obsolete toolchain that isn't being supported any more.
- focus on creating files on a "per feature basis". This helps on discoverability and maintainability of code
	- generic reusable components should be placed in a `components` directory that can in turn have separate subdirectories for specific functionalities e.g. `src/components/ui`, `src/components/featureA`, `src/components/featureB` and so on.
	- components that represent the top level of a route and encompass a specific feature should again be placed in directories focused on the feature they implement under the main `src` tag
	- focus on creating directories for your utility units of code (e.g. `src/utils`) and place units of code in there, again separating per feature beneath the `utils` directory

## Component Composition
- components *must not* be declared inside other components (inline). When re-rendering takes place the inline components will be re-calculated, which is expensive.
- business logic (processing) should be refactored to an external code file which should be imported as necessary.
- expensive calculations should be memoized. This includes functions that need to run when a component is being rendered. 
- components that are similar in appearance but have slightly different behaviour should be refactored to a component that takes the code that performs actions as properties (Dependency Injection).
- avoid `React.Context` for fast travelling state (e.g. forms or other data that changes frequently). Context should contain only data that change very rarely (e.g. visual theme)
- use libraries for validation - do not reinvent the wheel. The best proposition is `Zod`.
- if the global state does not have complexity it is better to use a library like `Justand` with atomic state responsibility. This ensures that less data is loaded into the component instead using a global store. Otherwise if Redux is necessary, *always* use `Redux Toolkit` and selectors to facilitate similar DX and data behaviour.
- in case of forms handling always opt to use a library to handle these interactions. The best proposal is `react-hook-form`. Alternatively for small forms and if you don't feel the need to add additional libraries to your code, opt to use `useReducer`. You can also create a new hook on to of it and factor it out to a different code file.
- avoid state when you are dealing with UX exclusive information. A visual element that only shows when a condition is met should only be present as design logic within the component rendering phase and not part of its state. 
- data fetching and general API calls should be handled through the use of a cache-able library. Please note that libraries need separate `GET` and `POST` functions that will be tied into a global "key" that is available to display/mutate data. In case of a lot of post/fetch code it is preferable to gather the actual functions in a separate file for easier maintenance.
	- `tanstack-query` offers a more comprehensive approach and better granularity on the cost of sometimes more boilerplate-y code. 
	- `useSWR` offers a more direct approach and should be preferred in case of simple data fetching cases. 

## SSR (Next.JS)
- opt in to use React Server Components to fetch initial data during server rendering phase. In case of lots of data fetching opt in to wrap all `fetch` or `axios.get` statements inside a `Promise.allSettled` and verify results. Avoid using `Promise.all` unless you don't care about gathering information on which API calls have failed.
- do not use layouts for authentication purposes.
- if a page's data need to be invalidate and recalculated, it's a good idea to ensure that the `Link` component that leads to this page has no-cache. 
- security should make use of Next.JS `middleware` - this way all interactions of the client with the server will go through this checkpoint. 

## Stylistic Principles
- css properties should be provided in css modules instead of inline styles unless it's a very slight modification of an existing visual behaviour.
- opt in to the use of css variables in case of global stylistic modifications. They can be altered via code during runtime and allow for massive stylistic changes in case of e.g. a differentiation of an existing css styling theme.

## General UX Principles

- accessibility should be a top-level priority. Investigate in either using UX component libraries that have this built in or investigate the basic ARIA principles.  
- web performance as a metric is a priority in terms of user retention and general experience. Investigate Time-To-Interactive metrics (e.g. by using Google Lighthouse or other similar tools) and refactor your code accordingly. This is another top-level priority in the case of public facing web sites. 
- avoid color combinations stylings that may cause problems to users with visibility problems. UX component libraries in general take care of this but their stylings may be overridden. Ensure that the web pages are accessible for all users. 

## General JS Code Principles
- DRY. Up to 2 times is okay to repeat code, anything more should be refactored out.
- when creating library / util files, opt to create namespaces and export them instead. This allows for both decluttering and also easier understanding of code.
- avoid classes and the class paradigm in JS code. Javascript is a language who leans heavily towards its functional aspect with functions being "first-class citizens", with lamdas, anonymous functions, functions as values, currying, etc.
- unless there are specific needs or there is not much performance overhead, simple loops (`for`) are better optimized than `foreach` or `for...of`.
- opt to use higher order functions in case of array iterations
- if in need of preserving array contents but need different array representations, opt to use the new `to...` methods (e.g. `toSorted`, `toSpliced`).
- if in need of array manipulations depending on the case (e.g. the last element, the element before last etc.) it would be more prudent to use `at` and `with` methods.
- copying objects (i.e. create new objects through the spread operator) should be used only when a new object reference is necessary (e.g. React's `useState`).
- if there exist lots of pages that share similar logic (e.g. a wizard that has pages with forms in it) it would be a good idea to refactor the logic to a form generator function or higher order component. This way the pages can be smaller and the actual code can transition to a "code as configuration". Be aware that this needs to be documented.
- units of code should be small and have meaningful names. A developer should be able to understand a unit of code without having to go x levels deep to understand what the unit of code does.
- opt to use objects instead of switch statements. An object which has functions as values allows for easier readability and maintainability instead of a switch statement.
- in case of error handling during development opt to use `console.error`.
- ensure that any `console` logging is always removed when commiting to production.


## Documentation

- document your code outside the code itself. Code should be self explanatory with meaningful names. Comments should be avoided unless absolutely necessary.
- document your code inside the project in a separate file. Ideally the code should be placed in a separate documentation folder inside the project structure and contain information on units of code, again by dividing documentation in a per feature directory and file hierarchy. Opt to use Markdown format for more formatting options. 
- a special exemption to the "no comments" rule would be the use of `JSDoc`  - especially if the project is not made in Typescript. This may depend heavily on any particular customer's needs.


