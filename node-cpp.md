# Node.js C++ Addons

The following document outlines Mapbox's approach to writing C++ modules for Node.js (often referred to as _addons_).

Node is integral to the Mapbox APIs. Sometimes at scale, though, Node becomes a bottleneck for performance. Node is single-threaded, which blocks execution. C++ on the other hand allows you to execute operations without clogging up the event loop. Passing heavy operations into C++ and subsequently into C++ workers can greatly improve the overall runtime of the code.

### Native Abstractions for Node.js (NAN)

To swing between Node and C++, the Node community maintains a project called [_NAN_](https://github.com/nodejs/nan) (Native Abstractions for Node.js) that simplifies running different versions of Node and, subsequently, v8. NAN is a header-only C++ library that provides a set of Macros for developing Node.js addons. Check out the [usage](https://github.com/nodejs/nan#usage) guidelines.

More examples of how to port C++ libraries to node can be found at [nodejs.org/api/addons](http://nodejs.org/api/addons.html). See https://nodesource.com/blog/c-add-ons-for-nodejs-v4/ for a detailed summary of the origins of Nan. And see http://blog.reverberate.org/2016/10/17/native-extensions-memory-management-part2-javascript-v8.html for a good introduction to v8 that is not specific to node.js.

### Why create an addon?

An addon is a viable solution for the following reasons:

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

Developing an addon requires Node.js, NPM, and a C++ compiler.

* makefile: home to all of the development commands for building binaries, installing dependencies, and running tests

* [node-pre-gyp](https://github.com/mapbox/node-pre-gyp): a module installed via NPM, tool that allows us to install and publish addons

* package.json `binary` object: sets the specific paths for bindings and remote publishing. Here's an example from @mapbox/vtinfo:

        "binary": {
          "module_name": "vtinfo",
          "module_path": "./lib/binding/",
          "host": "https://mapbox-node-binary.s3.amazonaws.com",
          "remote_path": "./{name}/v{version}/{configuration}/",
          "package_name": "{node_abi}-{platform}-{arch}.tar.gz"
        },

* common.gyp: sets your default configurations for building C++ binaries

* binding.gyp: sets your custom configurations for developing the library, including paths to dependencies, flags, and binding destinations       

All binaries are generated with node-pre-gyp's `build` command, which detects the system architecture automatically.

##### Including other C++ headers into your project

C++ headers can be installed in a few ways:

* Installed via Mason: use Mason to install a project, these can be installed into whichever folder you choose to host dependencies. Best practice is a `/deps` directory.
* Installed via NPM: Publishing headers to NPM allows them to be included in addons easily, since we are already using the NPM ecosystem. Header paths will point to the `/node_modules` folder or can include dynamically with an [`include_dirs.js`](https://github.com/mapbox/protozero/blob/master/include_dirs.js) file.
* Copied/pasted into a `/deps` directory.

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

### Publishing

##### Create binaires with node-pre-gyp

An addon can be published to NPM just like any other Node module. Unlike a standard Node module, an addon requires binaries to execute the code. If the binaries don't exist, they need to be built. User's may not have the tools necessary to compile C++ binaries on their system, like `gcc` or `clang`, so it's considered best practice to publish binaries to a public location. This greatly improves the speed at which they can install a module and use it. 

Node-pre-gyp does a lot of the heavy lifting for publishing binaries, using the `node-pre-gyp publish` command.

##### Publish binaries to AWS S3 with TravisCI

Check out [node-pre-gyp's docs](https://github.com/mapbox/node-pre-gyp#s3-hosting) about hosting and publishing binaries to s3.

### Versioning

When developing addons, versioning is extremely important. The NPM ecosystem allows modules to install different versions of modules depending on their dependencies, which works fine for javascript, but not great for binaries. **It's very important to ensure a project has only ONE version of a node addon** to prevent mismatching binaries from running simultaneously. This can be accomplished by making an addon a dependency of the main application, which lets NPM dedupe correctly.

Here's an example project that shows the potential disasters of running two binaries, [node-cpp-snafu](https://github.com/mapbox/versioning-node-snafu).

Check out or docs about [versioning a Node C++ library](https://github.com/mapbox/versioning-node-cpp).

### Miscellaneous

##### Naming Repositories

Depending on the reason why you are creating an addon library, your naming scheme should follow these general guidelines:

* If your project is a Node.js port (originally a C++ project), name it `node-{project name}`
* If your project is originally written in pure Node.js and you are porting to start using addons, name it `{project}-cpp`.

Example

Mapnik is a C++ library, named `mapnik`. Its Node.js interface is named `node-mapnik`.

## [Glossary](https://github.com/mapbox/cpp/blob/master/glossary.md#nodejs--c)