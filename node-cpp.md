# Node.js C++ Addons

* [Why create a node addon?](#why-create-a-node-addon)
* [How is a Node addon different than a C++ project?](#how-is-a-node-addon-different-than-a-c-project)
* [Native Abstractions for Node.js (NAN)](#native-abstractions-for-nodejs-nan)
* [Examples](#examples)
* [Developing addons](#developing-addons)
* [Where do I include other C++ libraries?](#where-do-i-include-other-c-libraries)
* [Versioning](#versioning)
* [Additional Resources](#additional-resources)

The following document outlines Mapbox's general approach to writing C++ modules for [Node.js](https://github.com/mapbox/cpp/blob/master/glossary.md#node) (often referred to as _addons_), and the _why_. Check out [node-cpp-skel](https://github.com/mapbox/node-cpp-skel), a skeleton library for creating a Node.js addon, to learn more about _how_ to create an addon. When we hit bottlenecks in JS/Node.js scripts that can be solved by high concurrency we will consider porting node modules to C++ to leverage the threadpool - https://www.joyent.com/blog/node-js-on-the-road-dc-young-hahn

Node is integral to the Mapbox APIs. Sometimes at scale, though, Node becomes a bottleneck for performance. Node is single-threaded, which blocks execution. C++ on the other hand allows you to execute operations without clogging up the event loop (learn more about the node event loop [here](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)). Passing heavy operations into C++ and subsequently into C++ workers can greatly improve the overall runtime of the code.

### Why create a node addon?

1. To port a C++ project to Node to expose a new interface for the tool (like Mapnik & Node Mapnik)
1. Improve performance at scale where Node becomes the bottleneck (i.e. concurrency)

**Concurrency**

Concurrency is the process of executing different pieces of the same process to allow for parallel execution of these pieces [[wikipedia](https://en.wikipedia.org/wiki/Concurrency_(computer_science))]. Node.js addons allow us to take advantage of concurrent operations within our node applications by reaching into more than just the [v8](https://github.com/mapbox/cpp/blob/master/glossary.md#v8) thread, but this can result in a few surprises. In the example below, we’ve passed data from our node application, into the v8 thread, and subsequently into our worker threadpool. Here there are multiple workers executing code at the same time. In our worker, we have the following line:

```
std::cout << "royal with cheese" << std::endl;
```

This will print `royal with cheese` to the terminal. In an application running this multiple times we’d expect the output to look like this:

```
royal with cheese
royal with cheese
royal with cheese
royal with cheese
// ... and so on
```

![not concurrent](https://mapbox.s3.amazonaws.com/cpp-assets/addon-hey-nonconcurrent.gif)

But when we start printing from within the threadpool all of these can start executing simultaneously.

![concurrent](https://mapbox.s3.amazonaws.com/cpp-assets/addon-hey-concurrent.gif)

When we run the script again from within the threadpool, here's the output:

```
royal with cheese
royraolry roawoyliya talwhl i  wtcwihhit ethceh hs ceeche
heseeeres
oseye
r
aolry oawrylioa tylwha i lwtc ihhwt eihcet hshcee he
cesheeres
oeeys
raeol
ry oawrylioa tylwha i lwtc ihhwt eihcet hshcee he
cesheeres
oeeys
raeol
ry oawrylioa tylwha i lwtc ihhwt eihcet hshcee he
cesheeres
oeeys
raeol
```

That’s a messy burger! 🍔 std::cout is logging at the same time and space is being filled concurrently. We’re literally seeing concurrency happen here–awesome! This makes testing pretty hard though. There are a few options to get your logs in order, such as adding a [mutex](http://en.cppreference.com/w/cpp/thread/mutex) to keep things straight. Since we’re just testing, we can tell our computer to only run this script with a single thread - forcing our application to run non-concurrently. We pass the following before running our script:

```shell
UV_THREADPOOL_SIZE=1 node index.js
```

And the output is

```
royal with cheese
royal with cheese
royal with cheese
royal with cheese
```

### How is a Node addon different than a C++ project?

A Node.js addon is still a Node module. Users still interact with it as if they are writing Javascript (i.e. `var awesome = require('awesome')`), but the library will tend to pass much of the logic into C++ workers, which are highly performant, then return information back into a javascript interface. Bottom line, the user of your library never has to write or interact with C++.

### Native Abstractions for Node.js (NAN)

To swing between Node and C++, the Node community maintains a project called [_NAN_](https://github.com/nodejs/nan) (Native Abstractions for Node.js) that simplifies running different versions of Node and, subsequently, v8. NAN is a header-only C++ library that provides a set of Macros for developing Node.js addons. Check out the [usage](https://github.com/nodejs/nan#usage) guidelines.

More examples of how to port C++ libraries to node can be found at [nodejs.org/api/addons](https://nodejs.org/api/addons.html). See https://nodesource.com/blog/c-add-ons-for-nodejs-v4/ for a detailed summary of the origins of Nan. And see http://blog.reverberate.org/2016/10/17/native-extensions-memory-management-part2-javascript-v8.html for a good introduction to v8 that is not specific to node.js.

### Examples

All of the following libraries are installable in a Node.js environment, but execute much of their logic in C++:

* [Node Mapnik](https://github.com/mapnik/node-mapnik) - creating map tiles
* [Node OSRM](https://github.com/Project-OSRM/node-osrm) - directions & routing
* [sqlite](https://github.com/mapbox/node-sqlite3) - asynchronous, non-blocking SQLite3 bindings
* [Node GDAL](https://github.com/naturalatlas/node-gdal) - geographic operations
* [vtquery](https://github.com/mapbox/vtquery) - query vector tiles
* [vtinfo](https://github.com/mapbox/vtinfo) - reads and returns general information about a vector tile buffer

### Developing addons

Developing an addon requires Node.js, NPM, and a C++ compiler. Check out node-cpp-skel's ["extended tour"](https://github.com/mapbox/node-cpp-skel/blob/master/docs/extended-tour.md) for an up-to-date and opinionated approach to developing an addon. Additinally, the repository has docs on [running benchmarks](https://github.com/mapbox/node-cpp-skel/blob/master/docs/benchmarking.md), [publishing binaries](https://github.com/mapbox/node-cpp-skel/blob/master/docs/publishing-binaries.md), and a breakdown of all the [necessary components](https://github.com/mapbox/node-cpp-skel/blob/master/docs/extended-tour.md#configuration-files) to make it run.

### Where do I include other C++ libraries?

One big bonus of developing a Node.js addon is that you can include other C++ code in your project, even if this code wasn't intended to be used via a Node.js interface. C++ headers can be installed in a few ways:

* Installed via [Mason](https://github.com/mapbox/cpp/blob/master/glossary.md#mason): use Mason to install a project, these can be installed into whichever folder you choose to host dependencies. Best practice is a `/deps` directory.
* Copied/pasted into a `/deps` directory (this is also referred to as "vendoring")
* Installed via NPM: Publishing headers to NPM allows them to be included in addons easily, since we are already using the NPM ecosystem. Header paths will point to the `/node_modules` folder or can include dynamically with an [`include_dirs.js`](https://github.com/mapbox/protozero/blob/master/include_dirs.js) file. **Note: this practice is no longer recommended.**

Depending on how a project is installed, the path to the header files will be different. These paths can be added to the `binding.gyp` file and will look like this:

```javascript
{
  'includes': [ 'common.gypi' ],
  'targets': [
    {
      'include_dirs': [
        '<!(node -e \'require("nan")\')', // included dynamically with an include_dirs.js file
        './node_modules/project/include/project.hpp', // pointing to node_modules directory
        './deps/project/project.hpp' // pointing to deps directory (installed manually or with mason)
      ]
    }
  ]
}
```

### Versioning

When developing addons, versioning is extremely important. The NPM ecosystem allows modules to install different versions of modules depending on their dependencies, which works fine for javascript, but not great for binaries. **It's very important to ensure a project has only ONE version of a node addon** to prevent mismatching binaries from running simultaneously. This can be accomplished by making an addon a dependency of the main application, which lets NPM dedupe correctly.

Following this convention will make it easier to know:
  - when a module might be affected by any of its newly published dependencies
  - how to version your module and its dependencies

##### Convention

*Using node-mapnik as an example*

1. To avoid installing older versions: **favor using tilde `~` over x-range `0.x` for your module's dependencies**
2. If a module *does* directly depend on node-mapnik as a library (and the module is *not* an app or service): **leave patch version flexible by using a tilde `~`.** This ensures that the module is flexible to patch-level node-mapnik updates automatically
3. If a module *does not* directly depend on node-mapnik, but instead depends on other node-mapnik-dependent modules: **use tilde `~` for those dependencies**
4. If a module *does not* directly depend on node-mapnik, but instead depends on other node-mapnik-dependent modules, and you want the node-mapnik-dependent modules to automatically increment on the minor-level: **you must also increment your module's version on the minor-level even if there are no API-breaking changes**

##### Example downstream application

The following scenario uses a Node.js application that relies on node addons (node-gdal and node-mapnik). Specifically, we need to upgrade one addon and downgrade another at the same time, due to breaking changes.
- The first addon includes bug fixes that we want (node-gdal).
- The second addon has breaking changes that we want to revert and stabilize (node-mapnik).

For example, this is the `package.json` for your app, Nacho, at breakage:

```
"name": "Nacho",
"version": "0.1.0",
"dependencies":
"mapnik": "3.5.x",
"gdal": "0.8.x"
```

You want to revert mapnik, but want to upgrade gdal. You would then change `package.json` to:

```
"name": "Nacho",
"version": "0.2.0",
"dependencies":
"mapnik": "3.4.x",
"gdal": "0.9.x"
```

We want to release new **minor** tags in this case, instead of **patch** tags. For instance:
- If instead of a minor release of Nacho, you tagged a patch release, it could lead to unexpected surprises for other apps that requires Nacho...
- Jane has an app that requires Nacho `"nacho": "0.1.x"`
- Jane automatically updates based on patch versions. This means she would get a new set of binaries within node-gdal `0.9.x` without knowing it, potentially causing breakages.

Best solution in this case:
Release a new Nacho **minor** version `0.2.0` containing the reverted stable node-mapnik and the updated node-gdal.

```
"name": "Nacho",
"version": "0.2.0",
"dependencies":
"mapnik": "3.4.x",
"gdal": "0.9.x"
```

Immediately after, release a new Nacho minor version `0.3.0` containing the newest debugged node-mapnik `3.5.x` and the updated node-gdal `0.9.x`:

```
"name": "Nacho",
"version": "0.3.0",
"dependencies":
"mapnik": "3.5.x",
"gdal": "0.9.x"
```

Another way to look at it:

| version  | dependency | dep version | description |
|---|---|---|---|
| `0.0.0` | node-mapnik   node-gdal | `3.4.x`   `0.8.x` | start here, with previous versions of these node C++ dependencies
| `0.1.0` | node-mapnik   node-gdal | `3.5.x`   `0.8.x` | we had to upgrade node-mapnik to it's latest and greatest, but node-gdal stayed the same
| `0.2.0` | node-mapnik   node-gdal | `3.4.x`   `0.9.x` | we ran into an issue with the `0.8.x` version of node-gdal, which is a dependency of node-mapnik - had to update node-gdal and therefore update an older version of node-mapnik for the fix, which required us to downgrade node-mapnik in this repo. This means we have flipped versions and can lead to duplicate C++ binaries. Instead of a `0.0.1` release of this repo, we can publish a quick, one-off in `0.2.0` that takes into account the flipped dependencies without hurting other repos. |
| `0.3.0` | node-mapnik   node-gdal | `3.5.x`   `0.9.x` | Now we can get back on track, with both modules updated.

### Additional Resources

 - https://nodejs.org/en/docs/guides/simple-profiling/
 - https://www.dynatrace.com/blog/understanding-garbage-collection-and-hunting-memory-leaks-in-node-js/
 - https://www.dynatrace.com/blog/how-to-track-down-cpu-issues-in-node-js/
