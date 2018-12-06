* [Standard compiler](#standard-compiler)
* [Standard C++ Library](#standard-c-library)

# Standard compiler

Clang++ is the primary compiler we use to build binaries (in mason) and test C++ code at Mapbox. Clang++ v7.0.0 is the latest stable version that we should be targeting.

## Installing clang++

### Debian/Ubuntu

Because clang++/llvm development moves fast it is impractical to depend on the packaged versions in Ubuntu LTS releases (Ubuntu Precise only has clang 3.4 and Ubuntu Trusty only has clang 3.5).

Recommendation: install with mason (see below).

### OS X

On OS X Apple ships frequent XCode updates, so staying up to date with latest clang++ is as easy as following the XCode updates in the app store.

### Mason

You can install clang++ v7.0.0 using [mason](https://github.com/mapbox/mason), which works the same on both OS X and Linux:

```
CLANG_VERSION="7.0.0"
mason install clang++ ${CLANG_VERSION}
export PATH=$(mason prefix clang++ ${CLANG_VERSION})/bin:${PATH}
which clang++
```

On older linux versions (like `Ubuntu Trusty` and older) to compile C++ programs with this version of clang you'll need to have the libstdc++ headers available. These can be fetched by installing `libstdc++-5-dev (which are a sub-package of g++)`.

### TravisCI

**OSX**

On Travis OS X machines you can upgrade xcode by supplying the most recent `xcode` value for the `osx_image` option. At the time of writing the latest is installable like:

```yml
matrix:
  include:
    - os: osx
      # https://docs.travis-ci.com/user/languages/objective-c/#Supported-OS-X-iOS-SDK-versions
      osx_image: xcode9.2
      compiler: clang
```

**Installing via Mason**

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
      env: CXX=clang++ CLANG_VERSION="7.0.0"
      addons:
        apt:
          sources: [ 'ubuntu-toolchain-r-test' ]
          packages: [ 'libstdc++-5-dev' ]
    - os: linux
      sudo: false
      compiler: "clang-debug"
      env: CXX=clang++ CLANG_VERSION="7.0.0"
      addons:
        apt:
          sources: [ 'ubuntu-toolchain-r-test' ]
          packages: [ 'libstdc++-5-dev' ]

install:
  - mkdir ./mason
curl -sSfL https://github.com/mapbox/mason/archive/v0.19.0.tar.gz | tar --gunzip --extract --strip-components=1 --exclude="*md" --exclude="test*" --directory=./mason
  - ./mason/mason install clang++ ${CLANG_VERSION}
  - export PATH=$(./mason/mason prefix clang++ ${CLANG_VERSION})/bin:${PATH}
  - which clang++
```

Note: see all the versions of clang++ available in Mason at https://github.com/mapbox/mason/tree/master/scripts/clang%2B%2B

### Installing other llvm tools

You may also wish to install other parts of llvm like llvm-cov, clang-format, or clang-tidy.

You can either install the `llvm` package that contains all the llvm tools, or for faster downloads you can install individual packages.

Installing clang-format via mason can be done like:

```bash
mkdir ./mason
curl -sSfL https://github.com/mapbox/mason/archive/v0.18.0.tar.gz | tar --gunzip --extract --strip-components=1 --exclude="*md" --exclude="test*" --directory=./mason
CLANG_VERSION="7.0.0"
./mason/mason install clang-format ${CLANG_VERSION}
export PATH=$(./mason/mason prefix clang-format ${CLANG_VERSION})/bin:${PATH}
which clang-format
```

# Standard C++ Library

### OS X

On OS X we use libc++: http://libcxx.llvm.org

- This is the default C++ standard library on OS X since 10.9.
- But libstdc++ also still exists on OS X (at least as of 10.12 where it is deprecated)
- It is safe to dlopen a module linked to libc++ library from executable linked to libstdc++
- It is not a good idea to link a module to both libraries

Apple did some incredible work to make both libc++ and libstdc++ live harmoniously on OS X. This would lead to crashes or undefined behavior on linux, but on OS X it works fine. So, for example, you are able to use node.js binaries that link to `libstdc++` with node c++ addons that link to `libc++`. We do this commonly so that a node c++ addon can use the latest c++11 or c++14 features even though the node binary does not. The node binary provided by https://nodejs.org at the time of this writing still links to libstdc++. This is because they want the binary to support versions as old as OS X 10.7 that predate the existence of `libc++` on the system. This is achieved by passing the `-mmacosx-version-min` which automatically adds `-stdlib=libstdc++` to the linking flags. See [this line](https://github.com/nodejs/node/blob/88323e874473d18cce22d6ae134a056919c457e4/common.gypi#L355). If `-mmacosx-version-min=10.7` is not passed then the default linking would use `-stdlib=libc++`.

### Linux

On Linux we use libstdc++: https://gcc.gnu.org/libstdc++

### libstdc++ details

 - The apt packages are tightly coupled with g++ version
 - It is forward compatible and uses "versioned" symbols: this means that you can build against an older version and still safely run against a newer version. This means that it is:
   - Always safe to constantly upgrade to the latest version in production/runtime
   - If you upgrade the version used to compile/link binaries then you MUST also use at least that minimum version at runtime. Otherwise you will see runtime errors like `version GLIBCXX_3.4.20 not found`.
