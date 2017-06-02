## C++ @ Mapbox

This is the place to start for developing and deploying C++ at Mapbox.

The C++ language and projects built on top of it are essential to the Mapbox platform.

  - [Mapbox GL Native](https://github.com/mapbox/mapbox-gl-native)
    - The core codebase shared between all the native frontends
    - [Mapbox Qt SDK](https://github.com/mapbox/mapbox-gl-native/tree/master/platform/qt/)
    - [Mapbox iOS SDK](https://github.com/mapbox/mapbox-gl-native/blob/master/platform/ios/) and [macOS SDK](https://github.com/mapbox/mapbox-gl-native/tree/master/platform/macos/) are partially written in Objective-C++, a hybrid language
  - Mapbox directions is based on [OSRM](https://github.com/Project-OSRM/osrm-backend)
  - Mapbox geocoding uses [carmen-cache](https://github.com/mapbox/carmen-cache) for indexes
  - Mapbox tile services (api-maps, api-gl) use [Mapnik](https://github.com/mapnik/mapnik) and [node-mapbox-gl-native](https://github.com/mapbox/mapbox-gl-native/tree/master/platform/node)
  - Mapbox scripts for processing OSM are often based on [libosmium](https://github.com/osmcode/libosmium)
    - [minjur](https://github.com/mapbox/minjur) creates geojson and [tippecanoe](https://github.com/mapbox/tippecanoe) creates vector tiles for https://osmlab.github.io/osm-qa-tiles/

When we hit bottlenecks in JS/Node.js scripts that can be solved by high concurrency we will consider porting node modules to C++ to leverage the threadpool: https://www.joyent.com/blog/node-js-on-the-road-dc-young-hahn

### Learning Resources

- Writing header-only library: https://github.com/mapbox/hpp-skel
- Writing node c++ module: https://github.com/mapbox/node-cpp-skel
- Writing c++ cli application: https://github.com/mapbox/cpp-cli-skel
- Effective C++ by Scott Meyers: [Amazon](https://amzn.com/0321334876)
- Effective Modern C++ by Scott Meyers: [Amazon](https://amzn.com/1491903996)
- Basic examples of using the compiler and linker directly [learn-c](https://github.com/springmeyer/learn-c)
- [Everything you wanted to know about move semantics](http://www.slideshare.net/ripplelabs/howard-hinnant-accu2014)
- [Efficient Optional Values](https://akrzemi1.wordpress.com/2015/07/15/efficient-optional-values/)
- [Eli Bendersky's C/C++ posts](http://eli.thegreenplace.net/tag/c-c)
- [Howard Hinnants blog](https://howardhinnant.github.io)
- [The overhead of abstraction in C/C++ vs. Python/Ruby](http://blog.reverberate.org/2014/10/the-overhead-of-abstraction-in-cc-vs.html)
- [Competitive Programmer's Handbook](https://cses.fi/book.html)

Find existing real-world code:

  - Compiled apps: https://github.com/search?q=org%3Amapbox+cpp
  - Header-only: https://github.com/search?utf8=%E2%9C%93&q=org%3Amapbox+hpp&type=Repositories&ref=searchresults
  - All @mapbox org repos using C++: https://github.com/search?l=C%2B%2B&q=org%3Amapbox&ref=searchresults&type=Repositories&utf8=%E2%9C%93
  
#### Gochas

 - Read this great description of how brittle the system of includes and headers are in C/C++: http://clang.llvm.org/docs/Modules.html#problems-with-the-current-model

### Standard compiler

Clang++ is the primary compiler we use to build binaries (in mason) and test C++ code at Mapbox.

Clang++ v3.9 is the latest stable version that we should be targeting.

### Installing clang++

#### Debian/Ubuntu

Because clang++/llvm development moves fast it is impractical to depend on the packaged versions in Ubuntu LTS releases (Ubuntu Precise only has clang 3.4 and Ubuntu Trusty only has clang 3.5).

Recommendation: install with mason (see below).

#### Mason

You can install clang++ v3.9 using [mason](https://github.com/mapbox/mason), which works the same on both OS X and Linux:

```
CLANG_VERSION="3.9.1"
mkdir ./mason
curl -sSfL https://github.com/mapbox/mason/archive/v0.9.0.tar.gz | tar --gunzip --extract --strip-components=1 --exclude="*md" --exclude="test*" --directory=./mason
./mason/mason install clang++ ${CLANG_VERSION}
export PATH=$(./mason/mason prefix clang++ ${CLANG_VERSION})/bin:${PATH}
which clang++
```

Notes:

  - `export CXX=clang++-3.9` also works

To compile C++ programs with this version of clang you'll need to have the libstdc++ headers available. These can be fetched by installing `libstdc++-5-dev (which are a sub-package of g++)`.


##### clang++ via mason on travis

Installing on travis specifically looks like:

```yml
language: generic

cache:
  directories:
    - $HOME/.ccache

matrix:
  include:
    - os: linux
      sudo: false
      compiler: "clang-release"
      env: CXX=clang++ CLANG_VERSION="3.9.1"
      addons:
        apt:
          sources: [ 'ubuntu-toolchain-r-test' ]
          packages: [ 'libstdc++-5-dev' ]
    - os: linux
      sudo: false
      compiler: "clang-debug"
      env: CXX=clang++ CLANG_VERSION="3.9.1"
      addons:
        apt:
          sources: [ 'ubuntu-toolchain-r-test' ]
          packages: [ 'libstdc++-5-dev' ]

install:
  - mkdir ./mason
curl -sSfL https://github.com/mapbox/mason/archive/v0.9.0.tar.gz | tar --gunzip --extract --strip-components=1 --exclude="*md" --exclude="test*" --directory=./mason
  - ./mason/mason install clang++ ${CLANG_VERSION}
  - export PATH=$(./mason/mason prefix clang++ ${CLANG_VERSION})/bin:${PATH}
  - which clang++
```

Note: Mason also provides a `clang++` v3.5.2 package. Request is by setting `CLANG_VERSION=3.5.2` above instead of `3.9.1`

All the available `clang++` packages can be seen at: https://github.com/mapbox/mason/tree/master/scripts/clang

Public documentation on this is also available at https://github.com/mapbox/mason/blob/master/scripts/clang/3.9.1/README.md

#### OS X

On OS X Apple ships frequent XCode updates, so staying up to date with latest clang++ is as easy as following the XCode updates in the app store.

On Travis OS X machines you can upgrade xcode by supplying the most recent `xcode` value for the `osx_image` option. At the time of writing the latest is installable like:

```yml
matrix:
  include:
    - os: osx
      # https://docs.travis-ci.com/user/languages/objective-c/#Supported-OS-X-iOS-SDK-versions
      osx_image: xcode8
      compiler: clang
```

### Installing other llvm tools

You may also wish to install other parts of llvm like llvm-cov, clang-format, or clang-tidy.

You can either install the `llvm` package that contains all the llvm tools, or for faster downloads you can install individual packages.

Installing clang-format via mason can be done like:

```bash
mkdir ./mason
curl -sSfL https://github.com/mapbox/mason/archive/v0.9.0.tar.gz | tar --gunzip --extract --strip-components=1 --exclude="*md" --exclude="test*" --directory=./mason
./mason/mason install clang-format 3.9.1
export PATH=$(./mason/mason prefix clang-format 3.9.1)/bin:${PATH}
which clang-format
```

### Standard C++ Library

#### OS X

On OS X we use libc++: http://libcxx.llvm.org

- This is the default C++ standard library on OS X since 10.9.
- But libstdc++ also still exists on OS X (at least as of 10.12 where it is deprecated)
- It is safe to dlopen a module linked to libc++ library from executable linked to libstdc++
- It is not a good idea to link a module to both libraries

Apple did some incredible work to make both libc++ and libstdc++ live harmoniously on OS X. This would lead to crashes or undefined behavior on linux, but on OS X it works fine. So, for example, you are able to use node.js binaries that link to `libstdc++` with node c++ addons that link to `libc++`. We do this commonly so that a node c++ addon can use the latest c++11 or c++14 features even though the node binary does not. The node binary provided by https://nodejs.org at the time of this writing still links to libstdc++. This is because they want the binary to support versions as old as OS X 10.7 that predate the existence of `libc++` on the system. This is achieved by passing the `-mmacosx-version-min` which automatically adds `-stdlib=libstdc++` to the linking flags. See [this line](https://github.com/nodejs/node/blob/88323e874473d18cce22d6ae134a056919c457e4/common.gypi#L355). If `-mmacosx-version-min=10.7` is not passed then the default linking would use `-stdlib=libc++`.

#### Linux

On Linux we use libstdc++: https://gcc.gnu.org/libstdc++

#### libstdc++ details

 - The apt packages are tightly coupled with g++ version
 - It is forward compatible and uses "versioned" symbols: this means that you can build against an older version and still safely run against a newer version. This means that it is:
   - Always safe to constantly upgrade to the latest version in production/runtime
   - If you upgrade the version used to compile/link binaries then you MUST also use at least that minimum version at runtime. Otherwise you will see runtime errors like `version GLIBCXX_3.4.20 not found`.


### Deploying code

#### Packaging binaries

  - Use [node-pre-gyp](https://github.com/mapbox/node-pre-gyp) for node C++ modules
  - Use [mason](https://github.com/mapbox/mason) for C++ libraries and command line (cli) applications

#### Upgrade libstdc++

For binaries to run properly they will need the latest libstdc++ version installed.

#### Using apt-get directly

```
sudo apt-get install -y software-properties-common python-software-properties || true
sudo add-apt-repository -y ppa:ubuntu-toolchain-r/test
sudo apt-get update -y
sudo apt-get install -y libstdc++-5-dev
```

NOTE: To upgrade apt packages you type `apt-get install <package name>` and not `apt-get upgrade <package name>`. In `apt` terms `upgrade` relates to upgrading all packages per https://linux.die.net/man/8/apt-get. This is not what you want. So to upgrade a single package you do as above and run `apt-get upgrade -y` (to get access to new packages) and then `apt-get install <package name>` to upgrade that single package.

##### Upgrade libstdc++ On Travis:

```yml
addons:
  apt:
    sources: [ 'ubuntu-toolchain-r-test' ]
    packages: [ 'libstdc++-5-dev' ]
```

##### Upgrade libstdc++ On Circleci:

```yml
dependencies:
     pre:
         - sudo apt-add-repository -y ppa:ubuntu-toolchain-r/test
         - sudo apt-get -y update
         - sudo apt-get -y install libstdc++-5-dev
```

#### Code coverage

To enable code coverage for C/C++ with clang++ you first need to compile your code in Debug mode and with specific flags to tell the clang++ compiler to instrument the binaries with coverage information.

The specific flags you use will depend on the tools you wish to use to display the results. There are two options: `gcov` format and `llvm-cov profile` format. We recommend using the `gcov` format to interact with services like coveralls and codecov and using the `llvm-cov profile` format for displaying code coverage results locally.

##### Using profile coverage format

1) First set the key compiler and linker flags:

```
export CXXFLAGS="-fprofile-instr-generate -fcoverage-mapping"
export LDFLAGS="-fprofile-instr-generate"
```

Set an environment variable to ask for the files generated during testing to be named by their `pid`. This allows multiple test runs to contribute to the final results.

```
export LLVM_PROFILE_FILE="code-%p.profraw"
```

2) Build your application in Debug mode.

3) Run the tests

4) Run llvm-profdata and llvm-cov to display results

The `llvm-profdata` tool is able to get all `.profraw` files (from one or more test runs) and merge them into a format suitable for `llvm-cov`.

```
llvm-profdata merge -output=code.profdata code-*.profraw
```

Then with `llvm-cov` you can either a) report of summary of coverage for all tracked files, 2) display the line-by-line coverage in your terminal, or 3) display line-by-line coverage in an html file

Summary:

```
llvm-cov report /path/to/your/binary -instr-profile=code.profdata -use-color
```

Terminal output:

```
llvm-cov show /path/to/your/binary -instr-profile=code.profdata /path/to/source_files/*.cpp -filename-equivalence -use-color
```

HTML output:

```
llvm-cov show /path/to/your/binary -instr-profile=code.profdata /path/to/source_files/*.cpp -filename-equivalence -use-color --format html > /tmp/coverage.html
```

Troubleshooting:

More details on the `llvm-cov show` command at http://llvm.org/docs/CommandGuide/llvm-cov.html#llvm-cov-show.

If you hit an error like `Unsupported instrumentation profile format version` or `Unsupported profiling format version` or `Failed to load coverage: Unsupported coverage format version` running `llvm-profdata` or `llvm-cov` that means you have a mismatch in the versions between the clang++ compiler and the llvm-cov or llvm-profdata tools. Make sure you are using all of these tools from mason and with consistent versions. You may need to set `CXX=/path/to/mason-provided/clang++` to solve this error.

If you hit an error like `error: B??? : No such file or directory` or `warning: The file 'B??? isn't covered.` that indicates that inside the directory of source files you pointed `llvm-cov` to there is some invalid file. This can occur if a file was not compiled into your binary or if the file completely lacks coverage. To workaround this, remove any files that completely lack coverage from the arguments you pass to `llvm-cov show`. I consider this a glitch/bug of llvm-cov and have reported the issue upstream: https://llvm.org/bugs/show_bug.cgi?id=30659


##### Using gcov coverage format

1) First set the key compiler and linker flags:

```
export CXXFLAGS="--coverage"
export LDFLAGS="--coverage"
```

2) Build your application in Debug mode.

3) Run the tests

4) Post results to codecov.io

Using `llvm-cov gcov` command.

```
curl -S -f https://codecov.io/bash -o codecov
chmod +x codecov
./codecov -x "llvm-cov gcov" -Z
```

TODO: test, document how to use the results of `llvm-cov gcov` locally with `gcovr`

Troubleshooting:

More details on the `llvm-cov gcov` command at http://llvm.org/docs/CommandGuide/llvm-cov.html#llvm-cov-gcov.


#### Debugging crashes

The first step is to view the [backtrace](glossary.md#backtrace) for the crash.

##### OS X

On OS X [backtraces](glossary.md#backtrace) are automatically generated after a crash and dropped into `~/Library/Logs/DiagnosticReports/`. OS X can also be configured to display a notification on any crash in native code with:

```bash
defaults write com.apple.CrashReporter UseUNC 1
```

##### Linux

On linux backtraces [backtraces](glossary.md#backtrace) are not automatically generated.

To enable them, use [logbt](https://github.com/mapbox/logbt/). This will ensure that backtraces are displayed for any crashes right when the program exits. You can use `logbt` on both Linux and OS X by launching your program with `logbt`:

```bash
logbt -- node index.js
```

If your backtraces don't have enough detail, then try running [debug binaries](glossary.md#debug-build) in production.

If backtraces don't provide enough detail to clearly identify the problem consider running [sanitized binaries](glossary.md#sanitized-build).

#### Sanitizers

Sanitizers work at runtime to watch your code for common mistakes and warn or throw when those mistakes are encountered. As opposed to static analysis (which operates at compile time) they are not designed catch errors that never happen. So test coverage is essential. Or running your code in real-world scenarios. The latter case is where the sanitizers really shine compared to other debugging tools because they can work with `-O1` optimization and don't slow down the runtime very much. Other tools, like valgrind, may slow down your program execution so much that actually reaching the bug in a long running process might take so long as to be untenable.

The Address sanitizer is one of the most useful because it can catch memory leaks (only on Linux however) and invalid use of memory that might cause crashes or undefined behavior.

To leverage `-fsanitize=address` on OS X you'll need a custom built clang because the apple/xcode provided command line clang++ does not support `-fsanitize=address` at the time of this writing (though it does work inside XCode). Also on OS X, if using `-fsanitize=address` outside of XCode, it is important to define `-D_LIBCPP_HAS_NO_ASAN` in your CXXFLAGS otherwise you might receive false positives for container overflows (see http://stackoverflow.com/a/38858905 for an explanation).

To leverage `-fsanitize=address` on Linux you'll want a very recent clang to ensure you a getting the best sanitizer results.

So, for both platforms it is recommended to [install clang++ via mason packages](#mason).

After installing your custom clang, for `make`-based build systems, do:

```
export CXXFLAGS="-fsanitize=address -D_LIBCPP_HAS_NO_ASAN"
export LDFLAGS="-fsanitize=address"
```

Then inspect the build output to ensure that the `-fsanitize=address` flag is showing up. You may need to pass `V=1` or `VERBOSE=1` to make systems to get the build output to show up.

For `cmake`-based systems it may be harder to pass these flags, so consult with a developer on the project that can help you.

### Versioning

When your node C++ module starts being depended upon by multiple downstream applications, start following https://github.com/mapbox/versioning-node-cpp.


### FAQ

#### Should I install `libstdc++6` instead of `libstdc++-5-dev`?

The `tl;dr` is yes you can - there is no harm - but don't please. Please install `libstdc++-5-dev` instead - it is more explicit.

The longer version is that installing the `libstdc++6` package is functionally the same as installing `libstdc++-5-dev`. The difference is that `libstdc++-5-dev`:

  - Brings in a few more headers (nothing so big to worry about)
  - If you need the ability to compile c++14 programs you need these headers
  - The `libstdc++-5-dev` package is wonderfully named!

Wonderfully named you say? Yes, it is named logically and that is a wonderful thing. The name clearly indicates that `libstdc++` is being installed. The version `5` indicates that you are getting the version compatible with g++-5 and its respective headers. And it is the development version so the version you use if you need libstdc++ headers (the files put into {prefix}/include` and the right libstdc++ library to be installed (the dynamic (aka shared) library put into `{prefix}/lib`).

The `libstdc++6` package name can be confusing. Note that there is no dash between the `++` and the `6` like there is between the `++` and the `5` in the dev package.

So why the `6`? It is the soname 6. Sonames are a linux way of speaking about binary library compatibility (ABI). The soname is not the version! The soname also does not really capture all you need to know about binary capability because libstdc++ also uses [versioned symbols](https://github.com/springmeyer/glibcxx-symbol-versioning). Because of versioned symbols you can live a free and exciting life always running the latest libc or libstdc++ without fear because it is forward compatible (binaries only use the versioned symbols they were compiled against).

#### So what version do you actually get if you install `libstdc++6` or `libstdc++-5-dev`?

It depends :) The important thing to remember is that unless you know better you want the latest version. If you really need to know then you can do:

```
apt-cache show libstdc++6
```

You will get the latest version packaged in https://launchpad.net/~ubuntu-toolchain-r/+archive/ubuntu/test/+packages for ubuntu precise and trusty. At the time of this writing the version is `v6.1.1`. Again, that `6` is not related to the soname.

Btw, if you ran `apt-get show libstdc++6` on ubuntu precise without the `ubuntu-toolchain-r` PPA installed then `apt-get install libstdc++6` would give libstdc++ `v4.6.3`.

If you hit a runtime error like `/usr/lib/x86_64-linux-gnu/libstdc++.so.6: version GLIBCXX_3.4.20' not found` it means you forgot to upgrade libstdc++6 to at least `v6.1.1`.

One caveat: To upgrade libstdc++ for binary programs that don't need to be compiled, just installing `libstdc++6` is enough. You don't need the extra headers brought in by `libstdc++-5-dev`. But there is no harm having the headers installed and being explicit will help make clear in the future when we require the g++-6 version of libstdc++ and that we've upgraded to it by requiring `libstdc++-6-dev`. 

For much more detail about which libstdc++ versions are available on which ubuntu versions (and upgradable via which packages) see https://github.com/springmeyer/glibcxx-symbol-versioning

#### I'm seeing a compiler error like "no template named 'foo' in namespace 'std'". What is that about?

If you hit an error like:

```
deps/mapbox/variant/include/mapbox/variant.hpp:558:44: error: no template named 'is_nothrow_default_constructible' in namespace 'std'; did you mean 'has_nothrow_default_constructor'?
    VARIANT_INLINE variant() noexcept(std::is_nothrow_default_constructible<first_type>::value)
                                      ~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
                                           has_nothrow_default_constructor
```

look a few lines down and you will see:

```
/usr/lib/gcc/x86_64-linux-gnu/4.6/../../../../include/c++/4.6/type_traits:737:12: note: 'has_nothrow_default_constructor' declared here
    struct has_nothrow_default_constructor
```

Notice that the headers from `g++` 4.6 are being used. This means that clang++ is looking for C++11 features but could not find them in the headers available from g++. Therefore that indicates that clang++ could not find headers to use from more g++ version more recent than 4.6 (Note: at least 4.8 or ideally 5 is needed for c++11 support). This is an indication that you need to upgrade your libstdc++-5-dev package. See above.
