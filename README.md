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

When we hit bottlenecks in JS/Node.js scripts that can be solved by high concurrency we will consider porting node modules to C++ to leverage the threadpool: https://www.joyent.com/blog/node-js-on-the-road-dc-young-hahn

### Learning Resources

- Writing header-only library: https://github.com/mapbox/hpp-skel
- Writing node c++ module: https://github.com/mapbox/node-cpp-skel
- Writing c++ cli application: https://github.com/mapbox/cpp-cli-skel
- Effective C++ by Scott Meyers: [Amazon](https://amzn.com/0321334876)
- Effective Modern C++ by Scott Meyers: [Amazon](https://amzn.com/1491903996)
- Basic examples of using the compiler and linker directly [learn-c](https://github.com/springmeyer/learn-c)
- [Everything you wanted to know about move semantics](http://www.slideshare.net/ripplelabs/howard-hinnant-accu2014)

Find existing real-world code:

  - Compiled apps: https://github.com/search?q=org%3Amapbox+cpp
  - Header-only: https://github.com/search?utf8=%E2%9C%93&q=org%3Amapbox+hpp&type=Repositories&ref=searchresults

#### Gochas

 - Read this great description of how brittle the system of includes and headers are in C/C++: http://clang.llvm.org/docs/Modules.html#problems-with-the-current-model

### Standard compiler

Clang++ is the primary compiler we use to build binaries (in mason) and test C++ code at Mapbox.

Clang++ v3.8 is the latest stable version that we should be targeting.

### Installing clang++

#### Debian/Ubuntu

Because clang++/llvm development moves fast it is impractical to depend on the packaged versions in Ubuntu LTS releases (Ubuntu Precise only has clang 3.4 and Ubuntu Trusty only has clang 3.5).

Recommendation: install with mason (see below).

#### Mason

You can install clang++ v3.8 using [mason](https://github.com/mapbox/mason), which works the same on both OS X and Linux:

```
CLANG_VERSION="3.8.0"
git clone --depth 1 https://github.com/mapbox/mason .mason
./.mason/mason install clang ${CLANG_VERSION}
export PATH=$(./.mason/mason prefix clang ${CLANG_VERSION})/bin:${PATH}
which clang++
```

Notes:

  - `export CXX=clang++-3.8` also works

To compile C++ programs with this version of clang you'll need to have the libstdc++-5-dev headers available (which are provided by g++-5):

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
      env: CXX=clang++ CLANG_VERSION="3.8.0"
      addons:
        apt:
          sources: [ 'ubuntu-toolchain-r-test' ]
          packages: [ 'libstdc++-5-dev' ]
    - os: linux
      sudo: false
      compiler: "clang-debug"
      env: CXX=clang++ CLANG_VERSION="3.8.0"
      addons:
        apt:
          sources: [ 'ubuntu-toolchain-r-test' ]
          packages: [ 'libstdc++-5-dev' ]

install:
  - git clone --depth 1 https://github.com/mapbox/mason .mason
  - ./.mason/mason install clang ${CLANG_VERSION}
  - export PATH=$(./.mason/mason prefix clang ${CLANG_VERSION})/bin:${PATH}
  - which clang++
```

Note: Mason also provides a `clang++` v3.5.2 package. Request is by setting `CLANG_VERSION=3.5.2` above instead of `3.8.0`

All the available `clang++` packages can be seen at: https://github.com/mapbox/mason/tree/master/scripts/clang

Public documentation on this is also available at https://github.com/mapbox/mason/blob/master/scripts/clang/3.8.0/README.md

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

### Standard C++ Library

#### OS X

On OS X we use libc++: http://libcxx.llvm.org

- This is the default C++ standard library on OS X since 10.9.
- But libstdc++ also still exists on OS X (at least as of 10.12 where it is deprecated)
- It is safe to dlopen a module linked to libc++ library from executable linked to libstdc++
- It is not a good idea to link a module to both libraries

Apple did some incredible work to make both libc++ and libstdc++ live harmoniously on OS X. This would lead to crashes or undefined behavior on linux, but on OS X it works fine. So, for example, you are able to use node.js binaries that link to `libstdc++` with node c++ addons that link to `libc++`. We do this commonly so that a node c++ addon can use the latest c++11 or c++14 features even though the node binary does not. The node binary provided by https://nodejs.org at the time of this writing still links to libstdc++. This is because they want the binary to support versions as old as OS X 10.7 that predate the existence of `libc++` on the system. This is achieved by passing the `-mmacosx-version-min` which automatically adds `-stdlib=libstdc++` to the linking flags. See [this line](https://github.com/nodejs/node/blob/88323e874473d18cce22d6ae134a056919c457e4/common.gypi#L355). If `-mmacosx-version-min=10.7` is not passed then the default linking would use `-stdlib=libc++`.

Troubleshooting: An error like `clang: warning: libstdc++ is deprecated; move to libc++ with a minimum deployment target of OS X 10.9` is expected on OS X >= 10.12 if you are building a node c++ addon against the nodejs provided binary and that c++ addon is not explicitly requesting linking to libc++ or overriding the ``-mmacosx-version-min` flag. In this case the default is being use from node core. This is harmless until Apple decides to remove `libstdc++` from an OS X version. It is going to continue to warn until the nodejs project drops support for OS X 10.7 by changing [this line](https://github.com/nodejs/node/blob/88323e874473d18cce22d6ae134a056919c457e4/common.gypi#L355) or the c++ addon overrides the `-mmacosx-version-min` flag [like this](https://github.com/mapbox/node-cpp-skel/blob/bf5fa91961c4027e709b557f5d3acd7eef8a7894/binding.gyp#L20).

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
sudo apt-get install -y software-properties-common python-software-properties
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
         - sudo -E apt-add-repository -y "ppa:ubuntu-toolchain-r/test"
         - sudo -E apt-get upgrade -y
         - sudo -E apt-get -yq --no-install-suggests --no-install-recommends --force-yes install libstdc++-5-dev
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


#### Debugging production crashes

See if you can replicate locally on OS X first. On OSX backtraces are automatically generated after a crash and dropped into `~/Library/Logs/DiagnosticReports/`. Maybe the backtrace there can give you enough detail to find a bug, fix it, re-deploy to staging and confirm the fix is ready for production.

If not then the next step is to ensure your production and/or staging stacks are generating backtraces.

On linux backtraces are suppressed by default. Yes, this is a serious inconvenience. For a node.js programmer this would be like having all javascript errors stop the program with nothing more than "Error". In the case of C++ you'll only see something like `segmentation fault` or `double free` or `terminate called after throwing an instance ...` when what you really want is a backtrace to show you were in the code the `segmentation fault` or `double free` or `terminate` actually happened.

To do this you need to tell the linux kernal to allow backtraces:

```
ulimit -c unlimited
```

After setting this, a crashing program that previously exited with just `segmentation fault`, should now report ``segmentation fault (core dumped)`. The `core dumped` part indicates that the `ulimit -c unlimited` worked and now core files are being produced. Core files are a unix way of snap-shotting program state in a way that can be queried for information. They are the raw material we can use to generate a backtrace. Now, to generate the backtrace we need to do two more things: 1) find the core file, 2) run gdb.

The core file is either 1) in the current working directory of the program that crashed or 2) at a system defined location that you can view by doing `cat /proc/sys/kernel/core_pattern`. On most Mapbox API machines this location is `/tmp/logbt-coredump` as [defined here](https://github.com/mapbox/logbt/blob/2276ff1c1650bf430ea7d82d2845316863e292d8/bin/logbt#L10).

Now install and run gdb to generate a backtrace. You can do this like:

```
gdb /path/to/your/program /path/to/corefile -ex "set pagination 0" -ex "thread apply all bt" --batch
```

Where `/path/to/your/program` must be an absolute path to the C or C++ binary that crashed. This could be `node` or some other C++ command line tool.

Then disect the backtraces to get a sense of the cause of the crash.

If you backtraces don't have enough detail, then try running Debug binaries in production to be able to generate better backtraces. Both node-osrm and node-mapnik provide Debug binaries using alternative travis jobs.

If backtraces don't provide enough to solve the problem consider running node instrumented with `-fsanitize=address` along with your C++ code instrumented the same.

For more details on how to get backtrace generation working on travis see https://github.com/springmeyer/travis-coredump.

#### Sanitizers

Sanitizers work at runtime to watch your code for common mistakes and warn or throw when those mistakes are encountered. As opposed to static analysis (which operates at compile time) they are not designed catch errors that never happen. So test coverage is essential. Or running your code in real-world scenarios. The latter case is where the sanitizers really shine compared to other debugging tools because they can work with `-O1` optimization and don't slow down the runtime very much. Other tools, like valgrind, may slow down your program execution so much that actually reaching the bug in a long running process might take so long as to be untenable.

The Addess sanitizer is one of the most useful because it can catch memory leaks (only on Linux however) and invalid use of memory that might cause crashes or undefined behavior.

To leverage `-fsanitize=address` on OS X you'll need a custom built clang because the apple/xcode provided command line clang++ does not support `-fsanitize=address` at the time of this writing (though it does work inside XCode).

To leverage `-fsanitize=address` on Linux you'll want a very recent clang to ensure you a getting the best sanitizer results.

So, for both platforms it is recommended to [install clang++ via mason packages](#mason).

After installing your custom clang, for `make`-based build systems, do:

```
export CXXFLAGS="-fsanitize=address"
export LDFLAGS="-fsanitize=address"
```

Then ensure inspect the build output to ensure that the `-fsanitize=address` flag is showing up. You may need to pass `V=1` or `VERBOSE=1` to make systems to get the build output to show up.

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

So why the `6`? It is the soname 6. Sonames are a linux way of speaking about binary library compatibility (ABI). The soname is not the version! The soname also does not really capture all you need to know about binary capability because libstdc++ also uses [versioned symbols](http://ftp.ntu.edu.tw/software/libs/glibc/hjl/compat/). Because of versioned symbols you can live a free and exciting life always running the latest libc or libstdc++ without fear because it is forward compatible (binaries only use the versioned symbols they were compiled against).

#### So what version do you actually get if you install `libstdc++6` or `libstdc++-5-dev`?

It depends :) The important thing to remember is that unless you know better you want the latest version. If you really need to know then you can do:

apt-get show libstdc++6

You will get the latest version packaged in https://launchpad.net/~ubuntu-toolchain-r/+archive/ubuntu/test/+packages for ubuntu precise and trusty. At the time of this writing the version is `v6.1.1`. Again, that `6` is not related to the soname.

Btw, if you ran `apt-get show libstdc++6` on ubuntu precise without the `ubuntu-toolchain-r` PPA installed then `apt-get install libstdc++6` would give libstdc++ `v4.6.3`.

If you hit a runtime error like `/usr/lib/x86_64-linux-gnu/libstdc++.so.6: version GLIBCXX_3.4.20' not found` it means you forgot to upgrade libstdc++6 to at least `v6.1.1`.

One caveat: To upgrade libstdc++ for binary programs that don't need to be compiled, just installing `libstdc++6` is enough. You don't need the extra headers brought in by `libstdc++-5-dev`. But there is no harm having the headers installed and being explicit will help make clear in the future when we require the g++-6 version of libstdc++ and that we've upgraded to it by requiring `libstdc++-6-dev`. 

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
