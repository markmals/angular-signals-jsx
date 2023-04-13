# Angular Signals JSX

This library is a fork of Ryan Carniato's [VueRX JSX](https://github.com/ryansolid/vuerx-jsx), which implements a [DOM Expressions](https://github.com/ryansolid/dom-expressions) runtime using the signals found in [`@angular/core` v16.0.0-next.4](https://github.com/angular/angular/tree/0dd5c4781119489758c6634102d73c268a38681e/packages/core/src/signals) (since versions after that are too tied into Angular's runtime to be useful on their own). This implementation provides considerably better performance than pairing Angular's signals with their dirty-checking Zone.js change detection methods.

It accomplishes this with using [Babel Plugin JSX DOM Expressions](https://github.com/ryansolid/dom-expressions). It compiles JSX to DOM statements and wraps expressions in functions that can be called by the library of choice. In this case `effect` wraps these expressions ensuring the view stays up to date. Unlike with Angular's ChangeDetector, only the changed nodes are affected and the whole tree is not re-rendered over and over.

See it as a top performer on the [JS Framework Benchmark](https://krausest.github.io/js-framework-benchmark/current.html).

To use call render:

```js
import { render } from "angular-signals-jsx";

render(App, document.getElementById("main"));
```

And include 'babel-plugin-jsx-dom-expressions' in your Vite config.

```js
import { defineConfig } from "vite";
import babel from "@rollup/plugin-babel";

export default defineConfig({
  plugins: [
    babel({
      exclude: "node_modules/**",
      babelHelpers: "bundled",
      extensions: ["ts", "tsx"],
      plugins: [
        [
          "jsx-dom-expressions",
          {
            moduleName: "angular-signals-jsx"
          }
        ]
      ]
    })
  ]
});
```

For TS JSX types add to your `tsconfig.json`:

```js
"jsx": "preserve",
"jsxImportSource": "angular-signals-jsx"
```

## Installation

```sh
> npm install angular-signals-jsx babel-plugin-jsx-dom-expressions
```

## Examples

- [Angular Signals JSX counter](https://stackblitz.com/edit/angular-signals-jsx-counter?file=main.tsx)
- [Vite starter kit](https://github.com/markmals/angular-signals-jsx-vite-starter)
- [JavaScript Frameworks Benchmark](https://github.com/markmals/js-framework-benchmark/blob/angular/frameworks/keyed/angular-signals-jsx/src/main.jsx)

## API

Angular Signals JSX works with function components. It also ships a specialize map function for optimal list rendering that takes an array as it's first argument.

```jsx
const list = signal(["Alpha", "Beta", "Gamma"]);

<ul>
  {map(
    () => list(),
    item => (
      <li>{item}</li>
    )
  )}
</ul>;
```

Angular Signals JSX also supports a Context API.

Alternatively this library supports Tagged Template Literals or HyperScript for non-precompiled environments by installing the companion library and including variants:

```js
import { html } from "angular-signals-jsx/html"; // or
import { h } from "angular-signals-jsx/h";
```

There is a small performance overhead of using these runtimes but the performance is still very impressive. Tagged Template solution is much more performant that the HyperScript version, but HyperScript opens up compatibility with some companion tooling like:

- [HyperScript Helpers](https://github.com/ohanhi/hyperscript-helpers) Use an element as functions DSL
- [Babel Plugin HTM](https://github.com/developit/htm/tree/master/packages/babel-plugin-htm) Transpile Tagged Template Literals to HyperScript

Further documentation available at: [Lit DOM Expressions](https://github.com/ryansolid/lit-dom-expressions) and [Hyper DOM Expressions](https://github.com/ryansolid/hyper-dom-expressions).
