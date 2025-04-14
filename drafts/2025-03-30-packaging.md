# Differences between CommonJS and ESM

_note: nodejs probably does a little bit of blurring the lines here. browsers are 100% strict w/ ESM rules. I'm not 100% sure what node lets you get away with._

In CommonJS `require()` is just another function. It happens asyncronously, files are required, downloaded, and parsed in the order tha the `require()` is found. `import()` is an asyncronous version, can be `await`, returns a `Promise`.

In ESM, `import ` (not `import()`, also in ESM) is a keyword not a function. It is always processed first. The spec says (and the browser def follows) that the application should collect each `import` statement and start the network/filesystem calls for each one, and when they're all downloaded, start executing. `import()` is also not really a function in ESM, be careful doing weird stuff with it, but it basically functions similiarly to CommonJS's `import()`.


# Direct to the web:

## CommonJS

Browsers know nothing of CommonJS let alone resolving 'bare identifiers' like `@babel/core`. That's a clientside NPM thing.

### Bundling

Roll everything into one file. It tries to shake-tree and create separate files- don't know what it uses to load those- `fetch()` probably. Bundling is also discussed below in detail.

### Browserify

Not much experience, but I think this turns your require statements into `fetch()` statements?

## ESModules

Browsers natively support ESM but they're a little picky. Your imports obviously can't touch anything CommonJS because then the browser just chokes.

Every import is a network call. So if you have a submodule that's devided into 1000 submodules for organization, but are never actuall delivered separately, it's best to bundle them before handle to avoid all those network calls.

Since you can't have any commonjs dependencies, and many packages don't provide both, bundling the commonjs into your code is a good idea.

The other issue is that all your dependencies have to be pure ESM, or else you have to bundle the dependency into your module directly.

Browsers know nothing of bare identifiers like `@babel/core` so everything must point to a specific file, indicated by a `<script type='importmap'>`.

### Plans for this:

More details below, but the goal here is to create a utility that can use a dependency-graph (ie a list of files ur application needs by following the `imports` and generate an `importmap` based off of
1. a `cdn:"value"` in package.json.
2. something that reads `node_modules`, pulls out only what you need for the web, and builds the importmap off of a `rootDir` you specify (so instead of `./node_modules/thing/index.js` it would be `https://example.com/vendor/thing/index.js` or whatever you want)
3. and of course let's you override with an already written importmap.

# Use with node

`node` understands both and sometimes lets you mix them. You can `import()` either. You can `import ` a commonjs file. You cannot `require()` a ESM Module (theoretically, sometimes, but in practice, I believe it is disallowed).

`node` also obeys `package.json` which can now include indicators if both ESM and cjs are available. (`"exports":{"import":"/path/to/.cjs","require":"/path/to/cjs"}`). There are other keys in the `package.json` like `"type":"module"`, `"module":"path/to/module"` but I'm not sure if they are necessary or otherwise how they play in with `"exports"` which does it for me.


# Bundling

Bundling uses the same internals as node for resolving- it follows the node rules. However: I find it often _fails silently_. I 100% plan on addressing that. Very unhelpful.

## Optimizations

### Chunking

Sometimes it splits your bundled output into several outputs. Not sure why or how to activate it, how it connects them in the browser, but if you have things where they're supposed to be in the folder I'm sure you'll be fine.

### Treeshaking

Linters will complain if you have an unused variable. Treeshaking is like that on steroids. It waits until all your files are bundled and then it looks for code that can't be reached. I think probably these algorithms are pretty conservative.

# Transpiling

Transpiling, being often a key concept in bundling as well... but I'm not talking about using `babel` to move from ES3033 to ES3 (aka what `tsc` does), I'm talking about just the EMS/Require plugins.

It is possible to transpile from ESM to CommonJS. I'm assuming it's an easier problem because ESM is stricter than commonjs. Not sure where it might fail, maybe something to do with using a top-level await in the wrong place.

# A New Concept

More on this later...
