# Architecture

The architecture of TinyBase is pretty straightforward. This guide runs through
the main file structure and principles.

The top level directory contains lots of configuration files: for the package as
a whole (`package.json`), for Jest (`jest.config,js`), for Prettier
(`.prettierrc`) and for ESLint (`.eslintrc.json`). TypeScript configuration is
_not_ in the top-level directory, but is co-located with the `src` and `test`
files independently.

`gulpfile.mjs` is an important file, since it describes all the build steps for
the project, many of which are described in the Developing TinyBase guide.

## src

The main source code is in the top-level `src` directory, where there is a pair
of files for each major module: the `.d.ts` file containing the Typescript
definitions (and the source of truth for the documentation), and the `.ts` file
containing the main logic.

Most modules have a similar pattern: a single creation function (such as the
createStore function in the case of the store module), and a major interface
that the returned object conforms to (such as the Store interface).

As well as those, there are a set of common functions and utilities in the
`common` subdirectory, implementations used by the persisters module in the
`persisters` subdirectory, and hooks and components for the ui-react module in
the `ui-react` subdirectory.

There is a top-level `tinybase.d.ts` and `tinybase.ts` pair to wrap everything
together into a single convenient package: these contain just imports.

## test

There are Jest unit tests for all modules in the `unit` subdirectory. The `perf`
subdirectory contains the performance tests. The `e2e` subdirectory contains
Puppeteer tests for checking all the demos on the website work.

The `jest` subdirectory contains some extensions that allow TinyBase to do
things like count and report the total number of assertions.

There is more about TinyBase's testing in the following Testing guide.

## site

This is the folder containing content and assets required for the TinyBase
website. `demos` and `guides` subdirectories are self-explanatory and contain
markdown files for site pages.

The `ui` folder contains some React components that are server-side rendered at
build time to populate the `docs` directory which is used as a straightforward
GitHub pages deployment.

API documentation comes from the `.d.ts` files by way of the TinyDocs library
(which is essentially a custom wrapper around TypeDoc). Other static assets like
CSS and JS are built into the final site from other folders in this directory.

## lib

Not checked in, but distributed via NPM, is a `lib` directory, which contains
the `.d.ts` type definitions, the compiled `.js` files, and their compressed
`.js.gz` equivalents. These three files exists for each of the TinyBase
submodules.

The `tinybase.js` and `tinybase.js.gz` files represent the master package of
everything together (except the ui-react module, which always remains a
standalone option). Since many of the submodules share compiled-in dependencies,
the master package is smaller to include than including all of the submodules
separately. However, for a very minimal set of modules, you may save size by
including them piecemeal.

In the root of the `lib` directory, all of these files are built to the
[`esnext`](https://esbuild.github.io/api/#target) target and the
[`esm`](https://rollupjs.org/guide/en/#outputformat) format. In addition, there
are subdirectories that contain builds of different types:

| Directory | Target | Format | Minified |
| --------- | ------ | ------ | -------- |
| .         | esnext | esm    | true     |
| ./debug   | esnext | esm    | false    |
| ./umd     | esnext | umd    | true     |
| ./cjs     | esnext | cjs    | true     |
| ./es6     | es6    | esm    | true     |
| ./umd-es6 | es6    | umd    | true     |
| ./cjs-es6 | es6    | cjs    | true     |

These build configurations are all defined in the `compileForProd` task in
`gulpfile.mjs`.
