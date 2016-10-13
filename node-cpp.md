# Node.js C++ Addons

The following document outlines Mapbox's approach to writing native abstractions for Node.js (anecdotally referred to as _addons_).

Node is integral to the Mapbox APIs. Sometimes at scale, though, Node becomes a bottleneck for performance. Node is single-threaded, which blocks execution. C++ on the other hand allows you to execute operations without clogging up the event loop. Passing heavy operations into C++ and subsequently into C++ workers can greatly improve the overall runtime of the code.

**Native Abstractions for Node.js (NAN)**

To swing between Node and C++, the Node community maintains a project called [_NAN_](https://github.com/nodejs/nan) (Native Abstractions for Node.js) that simplifies running different versions of Node and subsequently, V8. NAN is a header-only C++ library that provides a set of Macros for developing Node.js addons. Check out the [usage](https://github.com/nodejs/nan#usage) guidelines.

such as node-mapnik, node-osrm, and node-osmium. More examples of how to port C++ libraries to node can be found at nodejs.org/api/addons.html. See https://nodesource.com/blog/c-add-ons-for-nodejs-v4/ for a detailed summary of the origins of Nan.

### Why create an addon?

Create an addon is a viable solution for the following reasons:

1. To port a C++ project to Node to expose a new interface for the tool (like Mapnik & Node Mapnik)
1. Improve performance at scale where Node becomes the bottleneck.

### How is it different than a C++ project?

A Node.js addon is still a Node module. Users still interact with it as if they are writing Javascript (i.e. `var awesome = require('awesome')`), but the library will tend to pass much of the logic into C++ workers, which are highly performant, then return information back into a javascript interface. Bottom line, the user of your library never has to write or interact with C++.

### Examples

All of the following libraries are installable in a Node.js environment, but execute much of their logic in C++:

* [Node Mapnik](https://github.com/mapnik/node-mapnik) - creating map tiles
* [Node OSRM](https://github.com/Project-OSRM/node-osrm) - directions & routing
* [sqlite](https://github.com/mapbox/node-sqlite3) - asynchronous, non-blocking SQLite3 bindings
* [vtinfo](https://github.com/mapbox/vtinfo) - reads and returns general information about a vector tile buffer
* [Node GDAL](https://github.com/naturalatlas/node-gdal) - geographic operations

### Developing addons

**Makefile**

**package.json**

**Generating local binaries**

**node-pre-gyp**

**Writing & running tests**

### Publishing

##### Create binaires with node-pre-gyp

An addon can be published to NPM just like any other Node module. Unlike a standard Node module, an addon requires binaries to execute the code. If the binaries don't exist, they need to be built. User's may not have the tools necessary to compile C++ binaries on their system, like `gcc` or `clang`, so it's considered best practice to publish binaries to a public location. This greatly improves the speed at which they can install a module and use it. 

Node-pre-gyp does a lot of the heavy lifting for publishing binaries, using the `node-pre-gyp publish` command.

##### Publish binaries to AWS S3 with TravisCI

### Miscellaneous

##### Naming Repositories

Depending on the reason why you are creating an addon library, your naming scheme should follow these general guidelines:

* If your project is a Node.js port (originally a C++ project), name it `node-{project name}`
* If your project is originally written in pure Node.js and you are porting to start using addons, name it `{project}-cpp`.

Example

Mapnik is a C++ library, named `mapnik`. Its Node.js interface is named `node-mapnik`.

##### Including other C++ headers into your project

1. Why do we go into C++ from Node?
  1. Performance & Scale
  1. Library naming schemes for github repos
1. Examples 
  1. @ Mapbox
    1. Node OSRM
    1. Node Mapnik
    1. VT Shaver
    1. vtinfo
  1. elsewhere?
1. Building & Testing (for me)
  1. Build locally and run tests that execute against binaries
1. Publishing (for them & AWS)
  1. NPM, but we need binaries for the C++
  1. Why do we publish binaries?
  1. How do we publish binaries?
  1. How are binaries retrieved by others?
  1. How to publish in other Node or OS versions
1. Do it yerself
  1. Node C++ Skeleton
  1. node-pre-gyp
  1. C++ glossary
1. Including other C++ header libs into a project
1. Versioning a Node C++ library?
  1. What happens when you hit different binaries? Snafu!
  1. How do we prevent this @ Mapbox?

