# C++ @ Mapbox

The C++ language and projects built on top of it are essential to the Mapbox platform. This is the place to start for developing and deploying C++ at Mapbox. You'll find information about best-practices, gotchas, and helpful libraries/resources for beginning to develop in C++.

### Table of contents

- [General C++ @ Mapbox](docs/general.md) (standard compilers & libraries)
- [Installing & packaging C++](setup-and-packaging.md) (Mason, travis, etc)
- [Node.js addons](node-cpp.md) (writing C++ for Node.js modules)
- [Testing](docs/testing.md)
- [Code coverage](docs/coverage.md)
- [Troubleshooting](docs/troubleshooting.md) (profiling, debugging)
- [External learning resources](docs/learning-resources.md) (skeleton libraries, reading, videos)
- [Glossary](glossary.md)
- [FAQ](docs/faq.md) (and things that have no home)

### Guides

- [Structural metaprogramming](docs/structural-metaprogramming.md)
- [Strong types](docs/strong_types.md)

### Active C++ projects at Mapbox

- Map rendering with [Mapbox GL Native](https://github.com/mapbox/mapbox-gl-native), the core codebase shared between all of the native frontend SDKs, some of which are also in C++ like [Qt](https://github.com/mapbox/mapbox-gl-native/tree/master/platform/qt/), and the [macOS SDK](https://github.com/mapbox/mapbox-gl-native/tree/master/platform/macos/) or  [iOS](https://github.com/mapbox/mapbox-gl-native/blob/master/platform/ios/), which is in Objective-C++ (a hybrid language).
- Mapbox directions is based on [OSRM](https://github.com/Project-OSRM/osrm-backend)
- Mapbox geocoding uses [carmen-cache](https://github.com/mapbox/carmen-cache) for indexes
- Mapbox tile services use [Mapnik](https://github.com/mapnik/mapnik) and [node-mapbox-gl-native](https://github.com/mapbox/mapbox-gl-native/tree/master/platform/node)
- Mapbox scripts for processing OSM are often based on [libosmium](https://github.com/osmcode/libosmium). [Minjur](https://github.com/mapbox/minjur) creates geojson and [tippecanoe](https://github.com/mapbox/tippecanoe) creates vector tiles for [OSM QA tiles](https://osmlab.github.io/osm-qa-tiles/).
- Mapbox Vector Tile encoding, decoding, and analysis tools: [lib-vector-tile](https://github.com/mapbox/vector-tile), [protozero](https://github.com/mapbox/protozero), [vtzero](https://github.com/mapbox/vtzero), [mapnik-vector-tile](https://github.com/mapbox/mapnik-vector-tile), [vtvalidate](https://github.com/mapbox/vtvalidate), [vtquery](https://github.com/mapbox/vtquery)
