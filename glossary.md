# C++ Glossary

This is an opinionated glossary of terms developed by the Mapbox team.

The assumptions are:

**_performance matters_**

You are choosing to use C++ to write the most efficient code possible

**_robustness matters_**

You are choosing to use C++ to unlock the benefits of a statically typed language

**_multiple platforms matter_**

Your code will need to run across multiple linux or os x versions

**_binary distribution matters_**

You seek to distribute your code to users and clients via easily installable binaries (without them needing to know how to compile it)

* * *

The glossary covers these primary areas:

-   packaging
-   build systems
-   compilers and linkers
-   memory allocation
-   library design
-   binary distribution
-   profiling
-   debugging

It is not intended to be a complete reference on language or syntax terms. Learn more about language terms at:

-   A glossary by the creator of C++: <http://www.stroustrup.com/glossary.html>
-   A keyword glossary: <http://en.cppreference.com/w/cpp/keyword>

Contributions are welcome. To contribute, please:

-   Add a new section with `##`
-   Add a new term with `###`
-   If you add a new term or section, rebuild the table of contents by running `npm install && npm run toc`
-   If you add new terms and want review please create a PR and /cc @springmeyer

## Table of Contents

-   [Core concepts](#core-concepts)

    -   [posix](#posix)
    -   [API](#api)
    -   [signal](#signal)
    -   [I/O](#io)
    -   [crash](#crash)
    -   [libstdc++](#libstdc)
    -   [libc++](#libc)
    -   [source code](#source-code)

-   [Packaging](#packaging)

    -   [mason](#mason)
    -   [node-pre-gyp](#node-pre-gyp)

-   [Environment variables](#environment-variables)

    -   [CPP](#cpp)
    -   [CC](#cc)
    -   [CXX](#cxx)
    -   [LD](#ld)
    -   [CFLAGS](#cflags)
    -   [CXXFLAGS](#cxxflags)
    -   [LDFLAGS](#ldflags)

-   [Builds](#builds)

    -   [make](#make)
    -   [cmake](#cmake)
    -   [gyp](#gyp)
    -   [bazel](#bazel)
    -   [visual studio](#visual-studio)
    -   [ninja](#ninja)
    -   [out of source build](#out-of-source-build)
    -   [in-tree build](#in-tree-build)

-   [Compilers and linkers](#compilers-and-linkers)

    -   [development toolchain](#development-toolchain)
    -   [compiler](#compiler)
    -   [Front-end](#front-end)
    -   [linker](#linker)
    -   [linked](#linked)
    -   [linking](#linking)
    -   [linking order](#linking-order)
    -   [link time optimization](#link-time-optimization)
    -   [translation unit](#translation-unit)
    -   [object file](#object-file)
    -   [symbol](#symbol)
    -   [executable](#executable)
    -   [loadable module](#loadable-module)
    -   [library](#library)
    -   [precompiled library](#precompiled-library)
    -   [precompiled](#precompiled)
    -   [shared library](#shared-library)
    -   [static library](#static-library)
    -   [statically linked](#statically-linked)
    -   [dynamically linked](#dynamically-linked)
    -   [dynamically loaded](#dynamically-loaded)
    -   [dlopen](#dlopen)
    -   [header-only library](#header-only-library)
    -   [zero cost design](#zero-cost-design)
    -   [LLVM](#llvm)
    -   [gcc](#gcc)
    -   [clang](#clang)
    -   [g++](#g)
    -   [clang++](#clang-1)
    -   [calling application](#calling-application)
    -   [header](#header)
    -   [include](#include)

-   [Performance](#performance)

    -   [performance](#performance-1)
    -   [efficiency](#efficiency)
    -   [expressive](#expressive)
    -   [optimization technique](#optimization-technique)
    -   [memoization](#memoization)
    -   [small size optimization](#small-size-optimization)
    -   [performant](#performant)
    -   [responsive](#responsive)
    -   [latency](#latency)
    -   [response time](#response-time)
    -   [scalable](#scalable)
    -   [scalability](#scalability)
    -   [compiler optimization level](#compiler-optimization-level)
    -   [concurrency](#concurrency)

-   [Build modes](#build-modes)

    -   [DNDEBUG](#dndebug)
    -   [DDEBUG](#ddebug)
    -   [release mode](#release-mode)
    -   [release build](#release-build)
    -   [debug build](#debug-build)
    -   [debuggable release build](#debuggable-release-build)
    -   [sanitized build](#sanitized-build)
    -   [sanitizers](#sanitizers)
    -   [profiling build](#profiling-build)
    -   [problem of debugging release-crashes](#problem-of-debugging-release-crashes)
    -   [problem of profiling and compiler optimization levels](#problem-of-profiling-and-compiler-optimization-levels)

-   [Debugging and Profiling](#debugging-and-profiling)

    -   [Profiling](#profiling)
    -   [debug symbols](#debug-symbols)
    -   [Debugger](#debugger)
    -   [Tracer](#tracer)
    -   [core file](#core-file)
    -   [callstack](#callstack)
    -   [calltree](#calltree)
    -   [backtrace](#backtrace)
    -   [core_pattern](#core_pattern)
    -   [undefined behavior](#undefined-behavior)
    -   [static analysis](#static-analysis)
    -   [runtime analysis](#runtime-analysis)

-   [Memory concepts](#memory-concepts)

    -   [allocation](#allocation)
    -   [deallocation](#deallocation)
    -   [reallocation](#reallocation)
    -   [instantiate](#instantiate)
    -   [memory address](#memory-address)
    -   [address space](#address-space)
    -   [free store](#free-store)
    -   [new keyword](#new-keyword)
    -   [allocator](#allocator)
    -   [stack](#stack)
    -   [stack allocation](#stack-allocation)
    -   [heap allocation](#heap-allocation)
    -   [heap](#heap)
    -   [custom allocator](#custom-allocator)
    -   [memory locality](#memory-locality)

-   [Language concepts and keywords](#language-concepts-and-keywords)

    -   [inline keyword](#inline-keyword)
    -   [one-definition rule](#one-definition-rule)
    -   [inline-expands](#inline-expands)
    -   [inlined](#inlined)
    -   [declaration](#declaration)
    -   [definition](#definition)
    -   [Forward declaration](#forward-declaration)
    -   [implementation](#implementation)
    -   [pointer](#pointer)
    -   [reference](#reference)
    -   [noexcept](#noexcept)
    -   [bytes](#bytes)
    -   [bits](#bits)
    -   [structure packing](#structure-packing)
    -   [sizeof](#sizeof)
    -   [memory alignment](#memory-alignment)
    -   [statically typed](#statically-typed)
    -   [dynamically typed](#dynamically-typed)
    -   [public member](#public-member)
    -   [private member](#private-member)
    -   [protected member](#protected-member)
    -   [class](#class)
    -   [struct](#struct)
    -   [assembly](#assembly)
    -   [machine code](#machine-code)
    -   [implicit conversion](#implicit-conversion)
    -   [explicit](#explicit)
    -   [explicit conversion](#explicit-conversion)
    -   [strong type](#strong-type)
    -   [deterministic](#deterministic)
    -   [fits into register](#fits-into-register)
    -   [constexpr](#constexpr)
    -   [singleton](#singleton)
    -   [static initialization](#static-initialization)
    -   [global static](#global-static)
    -   [static member](#static-member)
    -   [static method](#static-method)
    -   [static data](#static-data)
    -   [macro](#macro)

-   [Node.js & C++](#nodejs--c)

    -   [Node](#node)
    -   [V8](#v8)
    -   [Node Addon](#node-addon)
    -   [Event loop](#event-loop)
    -   [Threadpool](#threadpool)
    -   [libuv](#libuv)

## Core concepts

### posix

A set of standards for maintaining compatibility between unix-like operating systems. Generally posix is synonymous with unix when it comes to what system APIs exist in C/C++ code to interface with the operating system. With minor exceptions POSIX APIs work the same on linux and osx.

### API

Application programming interface. This term is used broadly to describe how software and code can and should interact. In C++ programs an API describes a public set of functions that can be called, classes that can be [instantiated](#instantiate), or [static data](#static-data) that can be used.

### signal

Signals are a method of interprocess communication. When a program exits it returns an integer code. That code, if the program crashed, will encode an integer id of a signal. This offers a way for monitoring programs that crashed and why. The signals that result from a [crash](#crash) are listed at <http://en.cppreference.com/w/c/program/SIG_types>.

To know the exit code of a signal you add the `<signal id> + 128`. You can find the id of each signal at <http://www.comptechdoc.org/os/linux/programming/linux_pgsignals.html>.

More info at <https://en.wikipedia.org/wiki/Unix_signal>

### I/O

An I/O operation that "calls out" from the process to the underlying system. For example, accessing the file system, reading/writing to memory, or sending something over the network

I/O stands for "input/output".

### crash

A crash is a general term to describe when execution of the program exits in an unintended and unrecoverable way. There are a variety of reasons a program may crash, ranging from a bug that created a fatal error condition to another program killing your program. Each crash can be described by one of a known set of [signals](#signal) which map to a return code.

For example, a segfault (segmentation fault or violation) leads to a `SIGSEGV` signal, which is id `11`, and an exit code of `128+11` == 139.

### libstdc++

The [GNU implementation of the C++ STL](https://gcc.gnu.org/onlinedocs/libstdc++/).

Note that on OS X, `libstdc++` does not support C++11 or C++14, and so we use `libc++` instead.  ([Read more](https://github.com/mapbox/cpp#standard-c-library).)

### libc++

The [LLVM implementation of the C++ STL](https://libcxx.llvm.org/)

### source code

In C++ source code describes `.hpp` or `.cpp` files before they are compiled. Groups of source code passed to the compiler are called [translation units](#translation-unit).

## Packaging

### mason

Mason is a package manager for C++. It is able to install [precompiled libraries](#precompiled-library) and [executables](#executable) from binaries. And it is able to install the source code for [header-only libraries](#header-only-library). All packages end up in the `./mason_packages` folder in the current working directory.

Learn more [at the mason homepage](https://github.com/mapbox/mason).

### node-pre-gyp

node-pre-gyp is a package manager for [node C++ addons](https://github.com/mapbox/cpp/blob/master/node-cpp.md). It is able to install [pre-compiled loadable modules](#loadable-modules) (aka `.node` files) from binaries.

It is called `node-pre-gyp` because it stands in-front of [gyp](#gyp), the default build system used for node core and node c++ addons. When `node-pre-gyp` is used to download a pre-compiled module it skips needing to invoke [gyp](#gyp) for a source compile.

Learn more [at the node-pre-gyp homepage](https://github.com/mapbox/node-pre-gyp).

## Environment variables

Build tools on [posix](#posix) systems such as [make](#make), [cmake](#cmake) and [gyp](#gyp) pick up many of these environment variables and use them for compiling and linking your code. If you leave them blank it depends on the build system and your setup what you will get.

### CPP

The C Pre-Processor - the program reading your code and replacing header includes with their content and macros with their values

### CC

C Compiler - the program understanding the C language, taking a .c source code file and generating a .o object file out of it

Ignored by [cmake](#cmake) if `-DCMAKE_C_COMPILER` is passed.

### CXX

C++ Compiler - the program understanding the C++ language, taking a [translation unit](#translation-unit) and generating an [object file](#object-file) out of it

Ignored by [cmake](#cmake) if `-DCMAKE_CXX_COMPILER` is passed.

### LD

linker - the program taking multiple [object files](#object-file) and [libraries](#library) and generating an ELF file out of it on linux or a Mach-O file on OS X - the final binary

### CFLAGS

Compilation flags for the C compiler, think `-Wall -Wextra -pedantic -O2 -std=c99` to enable warnings, optimize the code and work with C99 source code

### CXXFLAGS

Same for C++, `-Wall -Wextra -pedantic -O2 -std=c++14`.

Ignored by [cmake](#cmake) if `-DCMAKE_CXX_FLAGS` is passed.

### LDFLAGS

linker flags, for more advanced linker tuning.

Note: not respected with [cmake](#cmake) which uses different flags depending on the type of binary being created:

-   CMAKE_EXE_LINKER_FLAGS (for [executables](#executable))
-   CMAKE_SHARED_LINKER_FLAGS (for [shared library](#dynamicshared-library))
-   CMAKE_MODULE_LINKER_FLAGS (for [loadable module](#loadable-module))

More info [here](https://stackoverflow.com/a/3544343).

## Builds

### make

A venerable build system only compatible with unix systems. Usually available by default on most unix systems without needing to be installed or upgraded. In this sense `make` is a bit like `bash`. It is the first tool many developers reach for when starting a project.

The configuration for make is a `Makefile`. It uses `tab` indentation. It is rare that you will ever encounter a `Makefile` that needs a specific version of `make`. This is likely because what `make` does is pretty simple and was hammered out a long time ago and has not changed much in recent versions. (But note that most `Makefile`s rely on [GNU Make](https://www.gnu.org/software/make/) features and will not work with a standard [POSIX Make](http://pubs.opengroup.org/onlinepubs/9699919799/utilities/make.html).)

For a project that only needs to build a few files `make` can be a good choice. For projects that need to build libraries or many files, [cmake](#cmake) is the better choice.

At Mapbox `make` is commonly used as the entry point for building a complex application. So, you might have a `Makefile` that actually calls out to another build system like [cmake](#cmake) or [gyp](#gyp). This allows the documentation to simply say: type `make` to build this project, while more complex things actually happen under the hood.

Learn more [at the make homepage](https://www.gnu.org/software/make).

### cmake

A modern, very sophisticated, build system that is cross-platform.

But cmake is not a build system in the traditional sense: it does not directly call out to the compiler or linker. Instead it is designed to generate build files in a variety of formats. For example, it is common to use `cmake` to generate configuration files for [make](#make) on unix systems and [Visual Studio](#visual-studio) on windows systems. Frequently the desired target is for `cmake` to generate files for [ninja](#ninja)

The configuration for cmake is `CMakeLists.txt`. It uses a very custom syntax that takes a while to learn, may bring about anger, but works and will scale to large projects.

The `cmake` project is written in C++ so it needs to be compiled itself. Often needs to be upgraded since you will encounter projects using `CMakeLists.txt` that need specific `cmake` features only available in recent versions.

Learn more [at the cmake homepage](https://cmake.org/).

### gyp

Also modern and cross-platform like [cmake](#cmake). The advantage is that `gyp` is written in `python` and is therefore easier to install and upgrade. The disadvantage is that google, the original maintainer of `gyp`, is no longer maintaining `gyp` actively. For this reason we recommend porting most projects written in `gyp` to [cmake](#cmake). Google, in the meantime, is porting projects to [bazel](#bazel).

The `gyp` build system is currently the default build system used by node.js (and therefore node-addons). See also [node-pre-gyp](#node-pre-gyp) which provides binary distribution support in front of `gyp`.

Learn more about gyp [here](https://gyp.gsrc.io)

### bazel

A build system, written in java, from Google: <https://bazel.build>. At this time (mid 2018) it appears rarely used outside Google, however one projects using it is [envoy](https://github.com/envoyproxy/envoy).

### visual studio

The windows GUI that supports "project" files that `cmake` can generate in order to build C++ files.

### ninja

A cross platform build system that is designed to be fast. Written by google and the chromium project, but now a great, general open source project.

### out of source build

A common build convention is to put into a custom directory structure all the results of all compiling and linking [source code](#source-code). For example, all [object files](#object-files) might be put at a path like `./build/Release/obj/`, all [libraries](#library) at `./build/Release/lib/`, or all [executables](#executable) at `./build/Release/bin`.

Each build system may use a different directory name and structure, but the general goal is to:

-   Keep the binary outputs separate from the source code
-   Version the path of the build results based on the build type (e.g. `Release` or `Debug`)

This approach is in contrast to an [in-tree build](#in-tree-build). It is advantageous to [in-tree build](#in-tree-build) because:

-   It makes it easy to clean up all compiled files just by removing a single directly. (no complex cleanup scripts are needed to track down all the .o files)
-   It makes it easy to create multiple binaries, potentially build with different build options, without overwriting previous binaries.

### in-tree build

An in-tree build describes when a build system compiles all [object files](#object-files) and writes them to the same directory structure where the [source code](#source-code) exists. In this case you might have [source code](#source-code) at `src/foo/bar.cpp` and the object file would end up at `src/foo/bar.o`.

## Compilers and linkers

### development toolchain

The toolset used to generate C++ code into [executables](#executable) for a computer. Typically consists of a [compiler](#compiler) and [linker](#linker).

All C++ code falls into the ["ahead-of-time" compilation](https://en.wikipedia.org/wiki/Ahead-of-time_compilation) category, which means code is compiled and linked separately from execution, unlike  ["just-in-time"](https://en.wikipedia.org/wiki/Just-in-time_compilation) compilation languages, like JavaScript.

### compiler

A program that converts C++ [source code](#source-code) into machine code (binary) files readable by a computer.

Compilers turn `.cpp` code into `.o` [object files](#object-file) and are often a [front-end](#front-end) to the [linker](#linker)

Example open source compilers are [clang](#clang) or [gcc](#gcc).

It is common to specify the [CC](#CC) and [CXX](#CXX) environment variables and many build systems ([make](#make) in particular) to customize the default C and C++ compiler that should be used.

### Front-end

The term front-end refers to when a command line tool `A` can be used in place of another command line tool `B` such that you call `B` through the interface of `A`.

### linker

A program that combines [object files](#object-file) into a single [executable](#executable) or [library](#library). Typically a step enacted by the [compiler](#compiler). The linker on unix systems is usually the `ld` command. Type `man ld` for more info or visit [the ld linux man page](https://linux.die.net/man/1/ld)

### linked

This term is used to describe the process when an [executable](#executable) or [library](#library) is told to be [dynamically linked](#dynamically-linked) to [library](#library).

### linking

A general term to describe the process of combining all [translation units](#translation-unit) and resolving the undefined [symbols](#symbol) with the defined ones.

Generally linking occurs when [object files](#object-file) are combined into a [library](#library).

### linking order

The process of [linking](#linking) involves resolving dependencies that your code might depend on, including both across the [translation units](#translation-unit) of your project and external dependencies. When dependencies are libraries, the linker handles resolving [symbols](#symbol) differently depending on a variety of factors including:

-   The order of the libraries in the command sent to the linker on Linux.
-   Whether those libraries are [static libraries](#static-library) or [shared libraries](#dynamicshared-library)
-   Custom linker flags like `-undefined dynamic` on OSX or `--start-group` on Linux

This matters when you are linking your code to [static libraries](#static-library) on Linux. Imagine that:

-   Your program is called `convert` and it depends on `libpng`
-   You are building against a static `libpng.a` from [mason](#mason)
-   `libpng.a` also depends on zlib (could be `libz.so` from the system or libz.a from [mason](#mason)).
-   Your build system correctly asks for `convert` to be linked to both libpng and zlib
-   But you hit a linking error on defined symbols for zlib

Why did you hit undefined symbols? Most likely it is because your build system invoked the linker (through clang+++ in this example) with the order of libraries like:

    clang++ -o convert -lz -lpng

This will fail on Linux, when `libpng` is a static library, because the linker on linux requires `-lz` to be listed **after** `libpng` because `libpng` depends on `zlib`.

This would fix the linking:

    clang++ -o convert -lpng -lz

Hence: libraries depended upon by other libraries need to come after the library that needs them.

Yes, this is really quite tedious and brittle on Linux. If you don't have control of your build system such that you can change it to fix the linking order, you may be stuck. So, another workaround is to disable this behavior by asking the Linux linker to recurse to look for dependencies across all listed libraries.

This would work:

    clang++ -o convert -Wl,--start-group -lz -lpng

Because it would tell the compiler to try looking for undefined symbols across the entire group rather than just in order. Note: technically you are supposed to denote the end of the group of libraries with `-Wl,--end-group`, but I've found that is optional.

See the `Static Libraries` section of <http://www.evanjones.ca/build-c.html> for another mention of how linking order matters.

### link time optimization

Link Time Optimization (LTO) describes a method for "intermodular" optimizations on code: which is when optimizations are applied across [translation units](#translation-unit) through coordination between the compiler and the linker. You'll also hear the term "interprocedural", "cross program", or "whole program" optimization which generally refer to the same concept: being able to optimize across [translation units](#translation-unit).

To understand why this is important we need to know that the compiler, by default, only applies [optimizations](#compiler-optimization-level) on code it sees and normally this is a single [translation unit](#translation-unit). So, if you have all your code in a single `cpp` file (and you are not statically linking any third-party libraries) you are essentially already benefiting from "whole program optimization". But we want modular code for best maintainability and readability, so when we split a single `.cpp` into multiple `.cpp` and effectively spread our code across multiple [translation units](#translation-unit), LTO is the way we can keep our code running as fast as possible.

Enabling LTO is done by:

-   adding `-flto` to your compiler (aka [cxxflags](#cxxflags)) and link flags (aka [ldflags](#ldflags))
-   ensuring that your linker supports the compilers LTO format (on linux this means installing `binutils-gold`)
-   ensuring you use the same compiler to compile all your code (no mixing of compiler versions)

Then your code should run as fast as possible, despite modularization into separate `.cpp` files. If you are linking third-party static libraries then you'll also want to compile them with `-flto`.

One downside exists however: LTO requires both your compiler and linker to do more work and this may slow down your build. For small projects this is unimportant. But when building complex projects this might cause your machine to run out of memory or your builds to take forever. This is the reason that significant effort has gone into a variety of different implementations for LTO in compilers. In particular, Google engineers have worked hard on a [technology called "Thin LTO"](https://clang.llvm.org/docs/ThinLTO.html) in [LLVM](#llvm). Thin LTO is enabled with `-flto=thin` and is designed to unlock intermodular optimizations without slowing down builds as much as other LTO implementations.

But LTO is a complex subject and there is a lot more happening under the hood with `-flto=thin`. Learn all about Thin LTO via Teresa Johnson's presentation at cppcon: <https://www.youtube.com/watch?v=p9nH2vZ2mNo>

Learn more about LTO in LLVM at <https://llvm.org/docs/LinkTimeOptimization.html>

### translation unit

The input to a compiler from which an [object file](#object-file) is created. Normally this is one or more files with the extension of `.cpp`, `c++`, or `.cc`.

### object file

An object file contains object code. Object code is what a [compiler](#compiler) produces. It contains symbols, compiled code, external symbols, and other [static data](#static-data). If you are compiling three C++ files (aka [translation units](#translation-unit)) – `one.cpp`, `two.cpp`, `three.cpp` – they will be compiled into three object files – `one.o`, `two.o`, `three.o`. These can subsequently be combined by a [linker](#linker) and turned into a [library](#library) or [executable](#executable).

Note: The file extension of object files is usually `.o`. You may also encounter `.os`.

### symbol

In C/C++ a symbol is the name embedded inside a binary for a function or class. For example, every [executable](#executable) will have a function in the code named `main()`. Once this function is compiled it will be available in the binary as a public symbol. For example, we can confirm this for the `python` binary using the `nm` command:

```bash
$ nm $(which python) | grep main
0000000100000f60 T _main
```

`nm` shows you the symbol table for an [object file](#object-files). [symbols](#symbol) are generally either:

-   `T` = this object file has the implementation of this symbol
-   `U` = this object file will need the symbol to be dynamically loaded before use

Including a header file that only had definitions of functions or classes gets you to the `U` state - this lets the [translation unit](#translation-unit) compile, but you can’t execute it because there will be undefined symbols. It must be [linked](#linked).

### executable

A binary file and/or program that can be run by a computer. This is the outcome of [compiling](#compiler) and [linking](#linker). Specifically, an executable has a `main()` function or _entry point_. A binary that does not have a `main()` entry point is likely a #

### loadable module

A loadable module is similar to a [shared library](#dynamicshared-library). Just like a [shared library](#dynamicshared-library) a loadable module is:

-   created by [linking](#linking)
-   [pre-compiled](#precompiled)
-   [dynamically loaded](<#dynamically loaded>).

But, whereas a [shared library](#dynamicshared-library) is [dynamically linked](#dynamically-linked) at startup, a loadable module is designed to be loaded sometime after startup, by the [calling application](#calling-application), with [dlopen](#dlopen).

Most plugin type interfaces in C/C++ are implemented as loadable modules. These loadable modules can have any extension, but most commonly use the `.so` extension like a [shared library](#dynamicshared-library) does on linux.

Advanced note: A loadable module may depend on [symbols](#symbol) from external libraries. When those symbols are also needed by the [calling application](#calling-application) that will load the loadable module, then it is common for the loadable module to be created without resolving (aka linking it to) those external symbols. This means that the loadable module is [linked](#linked) without [linking](#linking) all of its dependencies. It will have undefined symbols when it is loaded unless they are available already in the [address space](#address-space) of the [calling application](#calling-application) already. This will be possible if the the [calling application](#calling-application) has itself been either [statically linked](#statically-linked) or [dynamically linked](#dynamically-linked) to external libraries that provide those [symbols](#symbol).

### library

A set of reusable C++ code that can be shared across projects. Libraries can be organized very differently, but typically contain a set of header files (static library) or [pre-compiled binaries (dynamic/shared library)](#precompiled-library).

As opposed to an [executable](#executable), a library does not have a `main()` entry point and, instead, provide a set of functions and classes that can be called by other C/C++ libraries or executables. It must include at least a single [header file](#header) so the _calling application_ knows the definition of the interfaces provided. A library can be as static archives ([static library](#static-library)) or as a [shared library](#dynamicshared-library).

### precompiled library

A general term to describe a [library](#library) created from [precompiled](#precompiled) code. All libraries are created this way, but may be turned into different types of libraries depending on how they should be used. Most commonly a precompiled library is either a [shared library](#shared-library) or [static library](#static-library).

### precompiled

A term used to describe something created from compiling code that defines an [API](#API). Something precompiled is assembled from a set of one or more C++ files (aka [translation unit](#translation-unit)) turned into [object files](#object-files).

### shared library

A shared library is a type of [precompiled library](#precompiled-library). It required at link time and runtime by applications that depend on it. This means it has already been [compiled](#compiler) into machine-readable code and is just required alongside your code when it executes.

Also known as a dynamic library.

### static library

A static library is a type of [precompiled library](#precompiled-library). It is only required at link time. When a static library is linked to another application all of its [object files](#object-file) are put into that application's executable. This step is often called "static linking". The [source code](#source-code) of a static library looks identical to a shared library (both contain headers and .cpp files). The only difference is the way it is built.

### statically linked

A program can statically link a [static library](#static-library). This means that no external library is needed at runtime. Instead all the [symbols](#symbol) needed are available in the binary already.

### dynamically linked

A [shared library](#shared-library) can be dynamically linked to another binary (either an [executable](#executable) or another [shared library](#shared-library)). The link happens during a build and is done by the system linker (usually the `ld` program or a compiler). This creates a dependence on this [shared library](#shared-library) that is resolved at startup of the program linking it. This runtime resolution at startup is done by the [operating systems loader](https://en.wikipedia.org/wiki/Loader_(computing)).

### dynamically loaded

A general term to describe a binary that is loaded either of these two cases:

-   A [library](#library) loaded into the [address space](#address-space) of a program by [dynamic linking](#dynamically-linked)
-   A [loadable module](#loadable-module) loaded into the [address space](#address-space) of a program by [dlopen](#dlopen).

### dlopen

The `dlopen` api is a [POSIX api](#posix) API available on linux and osx (on windows a similar API is `LoadLibraryA`) that is able to dynamically load a [shared library](#dynamicshared-library) or [loadable module](#loadable-module).

It is often used with [loadable modules](#loadable-module) to implement plugin systems to C++ programs.

It is often used with pure C [shared libraries](#dynamicshared-library) and [the libffi foreign function interface library](https://en.wikipedia.org/wiki/Libffi) to implement language bindings to C libraries.

More info at [this man7 page](http://man7.org/linux/man-pages/man3/dlopen.3.html)

### header-only library

Used to describe when code is organized such that all of the [source code](#source-code) is in the .hpp file such that:

-   No cpp files need to be compiled
-   To use the library, no library needs to be linked (just [using `#include <the header>`](#include) is enough

A well organized header-only library is rarely in a single header, but rather can be accessed by both:

-   A single header that includes all other headers
-   Public access to each individual header that implements discrete functionality with the aim of [zero cost design](#zero-cost-design).

### zero cost design

When library headers are organized in discrete parts such that downstream users can include just the functionality they need and nothing more. Zero cost design indicates there is no extra cost beyond the cost that is unavoidable. Zero cost design is [separation of concerns](https://en.wikipedia.org/wiki/Separation_of_concerns) applied to C++ libraries. It is particularly important for [header-only libraries](#header-only-library) but also is relevant to [precompiled libraries](#precompiled-library).

But really, what do we mean by "cost"?

The motivation is to avoid two types of cost:

-   adding extra compile time to downstream builds because major chunks of code are present in a header even when not used by consumers.
-   having headers pull in other headers that might have external dependencies which are not needed.

### LLVM

Low level virtual machine - a suite of tools that [clang++](#clang++) depends on.

### gcc

The gnu C compiler. See also [g++](#g++).

### clang

The `clang` is the same as [clang++](clang++) except compiles C code instead of C++. Usually you should be using `clang++` even if you are compiling pure C.

### g++

The gnu C++ compiler. Very similar to bu licensed differently and slower to compile C++ templates.

### clang++

The recommended [C++ compiler](#compiler) used a Mapbox. It is open source and packaged in [mason](#mason).

### calling application

A term used to describe the application that depends on the [API](#API) of another application.

### header

A file with the `.hpp` or `.h` file extension.

### include

Pronounced "hash include". This is the line of source code that looks like `#include <iostream>` at the top of a C++ file. This syntax includes a header and allows you to utilize the code defined in the included header file.

Literally speaking, [the compiler will "replace" the #include line with the actual contents of the file you're including when it compiles the file.](http://www.cplusplus.com/forum/articles/10627/).

There are [a couple ways](https://stackoverflow.com/questions/21593/what-is-the-difference-between-include-filename-and-include-filename/21594#21594) to include headers:

-   Using carrots, ex: `<nan.h>` --> look for header in the compiler search paths
-   Using quotes, ex: `"hello.hpp"` --> look for header in location relative to this file

## Performance

### performance

How quickly a program does its work. Improving performance involves doing work faster by optimizing code to be more [efficient](#efficiency) through code changes or [compiler optimizations](#compiler-optimization-level). But ultimately how fast a program runs is also constrained by the system resources and how long certain operations take on a computer: <https://gist.github.com/jboner/2841832>.

Performance in concurrent systems usually refers to [response time](#response-time) for a single request. When we describe the performance of systems under load we usually are referring to [scalability](#scalability)

### efficiency

How much work is required by a task. Improving efficiency requires doing less work and results in [performance improvements](#performance). An efficient program is one which does the minimum (that we're aware of) amount of work to accomplish a given task.

-   See also: CppCon 2014: Chandler Carruth "Efficiency with Algorithms, Performance with Data Structures" <https://youtu.be/fHNmRkzxHWs?t=754>

### expressive

Exposing intent clearly through programming language constructs. Not resorting to boiler-plate code and/or over-commenting to achieve it.

### optimization technique

Changing code to increase [efficiency](#efficiency) or [performance](#performance).

### memoization

An [optimization technique](#optimization-technique) that reduces the amount of work needed and increases [efficiency](#efficiency) by storing the results of expensive function calls or data access.

### small size optimization

Also known as SSO, this refers to when [stack allocation](#stack-allocation) is used to make data structures extremely fast. This strategy generally works by writing a custom class which:

-   Has a data member is used to store data.
-   That data member is not dynamically allocated unless the container needs to grow to a large size.
-   This saves on many allocations which often end up being a bottleneck.

This can be described as using "hybrid data structures".

This is also sometimes referred to as SBO (Small Buffer Optimization).

This is such an important optimization it is central to how the llvm compiler is written. See Chandler Carruth's talk at CppCon 2016 that covers this: “High Performance Code 201: Hybrid Data Structures" <https://youtu.be/vElZc6zSIXM?t=157>.

This optimization was applied to `std::string` in libstdc++ (<https://gcc.gnu.org/ml/libstdc++/2014-11/msg00111.html>)

It is also central to why Javascript is fast in node.js and chrome. V8 uses SSO for objects like [SmallOrderedHashTable and more](https://github.com/v8/v8/blob/fe598532ec1317e8b85343133be9fb708e07bd2e/src/objects/hash-table.h#L613-L649).

See also [this](https://stackoverflow.com/questions/10315041/meaning-of-acronym-sso-in-the-context-of-stdstring/10319672#10319672) and [this](https://akrzemi1.wordpress.com/2014/04/14/common-optimizations) for more about SSO.

### performant

Not yet an official word in English. Usually meant to refer to a program that performs well enough by some measure. Prefer describing a program as either having acceptable [performance](#performance) or acceptable [efficiency](#efficiency). Or consider if your program is better described as fast, [responsive](#responsive), or [low latency](#latency).

See also <https://english.stackexchange.com/a/196609>

### responsive

A term used to describe when a program or API has a quick [response time](#response-time).

See also <https://en.wikipedia.org/wiki/Responsiveness>

### latency

A term used to describe [response time](#response-time). A low latency system responds quickly. Systems that are low latency even under increased load (more requests) have the potential to be highly [scalable](#scalable).

See also <https://en.wikipedia.org/wiki/Latency_(engineering)>

### response time

The time taken between a request and response.

### scalable

Systems where [performance](#performance) is maintained under increasing load.

### scalability

How [scalable](#scalable) a system is by some measure of load.

### compiler optimization level

[Compilers](#compiler) transform code into binaries that can run on various platforms. But they also optimize that code in specific ways depending on the `optimization level`.

The `optimization level` impacts both how fast the resulting binaries will run, their size, and how much diagnostic information we can learn about the running binary through profiling.

The most important reason to understand `optimization levels` is to understand the larger concept of [build modes](#build-modes) including:

-   [release builds](#release-build)
-   [debuggable release builds](#debuggable-release-build)
-   [debug builds](#debug-build)
-   [profiling builds](#profiling-build)
-   [sanitized builds](#sanitized-build)

There are hundreds of different optimization options inside compilers. Luckily we don't need to know about the details because the common open source compilers (`clang++` and `g++`) provide a simplified "O" interface. You simply pass `-ON` where `O` is a capitol O and `N` is a number between `0` and `3`.

-   `-O0` - "oh zero" disables optimization.

    -   The program will run very very slowly and the resulting binary will likely be much larger (no dead code removal).
    -   The advantage is that [backtraces](#backtrace) to be much more detailed because inlining and other optimizations are disabled that would otherwise shrink the size of the [callstack](#callstack).
    -   A [backtrace](#backtrace) for a crash will likely show the exact line number where a crash happened when also built with `-g`.

-   `-O1` - "oh one" is moderately optimized.

    -   Not all functions will be inlined, but the program should still run reasonably fast and dead code will be removed.
    -   This level is a good compromise when you want good [backtraces](#backtrace)(when also built with -g) but not awful speed.

-   `-O2` - "oh two" is optimized, but should not explode code size

    -   The program will run nearly as fast as possible and the resulting binary will be small
    -   [Backtraces](#backtrace) will be less detailed due to inlining

-   `-O3` - "oh three" enables most all optimizations inside the compiler.

    -   The program will run as fast as possible and the resulting binary will be smaller (except where inlining might increase its size somewhat).
    -   [Backtraces](#backtrace) will be less detailed due to inlining

-   `-Os` - "oh s" enables optimizations for speed, but not at the cost of code size.
    -   The program may run slower than `-O2` or `-O3`, but how much slower is hard to determine without extensive testing.

Compilers are rapidly adding more and more optimizations and shifting around which internal optimizations are enabled in which "O" level. See also:

-   [clang](https://clang.llvm.org/docs/CommandGuide/clang.html#cmdoption-o0)
-   [gcc](https://gcc.gnu.org/onlinedocs/gcc/Optimize-Options.html)
-   [discussion by clang devs](http://clang-developers.42468.n3.nabble.com/Meaning-of-LLVM-optimization-levels-td4032493.html)
-   [a listing](http://llvm.org/docs/Passes.html) of all the internal compiler optimizations in clang++ which you don't need to know specifically, but which are under-the-hood of the "O" levels.
-   [a detailed summary](http://stackoverflow.com/a/15548189) of which internal optimizations are grouped in which "O" level across clang++ releases.

### concurrency

Concurrency is the process of executing different pieces of the same process to allow for parallel execution of these pieces \[[wikipedia](https://en.wikipedia.org/wiki/Concurrency_(computer_science))]. See [more info here](https://github.com/mapbox/cpp/blob/master/node-cpp.md#why-create-a-node-addon).

## Build modes

### DNDEBUG

`DNDEBUG` stands for `do not debug` or `define no debug` and is a [preprocessor macro](#macro) or `#define`. It is passed as an option to a [compiler](#compiler).

To define it pass `-DNDEBUG` to the compiler. It is the opposite of [`-DDEBUG`](#DDEBUG) which stands for `yes, please, do debug`.

The `-DNDEBUG` flag internally tells the compiler to remove [assert](http://en.cppreference.com/w/cpp/error/assert)s from the code. This allows the program to run faster (less expensive checks) at the cost of no longer preventing the program from stopping on `assert`s that might protect from [undefined behavior](#undefined-behavior).

:warning: Do not be tempted to write unit tests using `assert` unless you are 100% confident your build system sets `-DDEBUG`. Otherwise your tests will not be testing what you think because the `assert`s may be gone!

Also, sometimes programs hook into the `DNDEBUG` `#define` to apply their own custom behavior. For example `protozero` changes behavior depending if `-DNDEBUG` is set. If it is set then C++ exception are thrown only when something terrible goes wrong parsing. If `-DNDEBUG` is not set then `protozero` assumes you want `-DDEBUG` and enables `asserts` that help catch bugs that might not ever show up with `-DNDEBUG` (aka [release mode](#release-mode)). More details on this example at <https://github.com/mapbox/protozero/blob/master/doc/tutorial.md#asserts-and-exceptions-in-the-protozero-library>.

See also: <http://en.cppreference.com/w/cpp/error/assert>

### DDEBUG

`DDEBUG` stands for `yes, please, do debug` or `define debug` and is a [preprocessor macro](#macro) or `#define`. It is passed as an option to a [compiler](#compiler).

To define it pass `-DDEBUG` to the compiler. It is the opposite of [`-DNDEBUG`](#DNDEBUG) which stands for `do not debug`.

When defined [assert](http://en.cppreference.com/w/cpp/error/assert) in the code will be kept and enabled.

### release mode

Release mode is when a [release build](#release-build) is run.

### release build

A release build describes a C++ binary built with a high [compiler optimization level](#compiler-optimization-level) and the `-DNDEBUG`flag. A release build is often built without flags enabling [debug symbols](#debug-symbols) (when developers want the smallest possible binaries). But because [debug symbols](#debug-symbols) do not impact program speed, some projects and developers prefer to include flags enabling [debug symbols](#debug-symbols) even in release builds.

### debug build

A debug build describes a C++ binary built with the [`-DDEBUG`](#DDEBUG) flag and with lower [compiler optimization levels](#compiler-optimization-level). A debug build is also usually built with flags enabling [debug symbols](#debug-symbols)

Debug builds run slower and may assert on hidden bugs in the code. They are very useful for testing and finding problems in the code.

### debuggable release build

Like a [profiling build](#profiling-build), a debuggable release build is a hybrid between a [release build](#release-build) and a [debug build](#debug-build). A  debuggable release build tries to address the [problem of debugging release-crashes](#problem-of-debugging-release-crashes).

A build like this should:

-   Use the [highest compiler optimization level](#compiler-optimization-level)
-   Enable [debug symbols](#debug-symbols) by passing `-g`.

This is similar to a [profiling build](#profiling-build) but without any extra flags that might hurt [performance](#performance).

Note: Developers that use cmake can automatically build this way by passing the `RelWithDebInfo` value to the [CMAKE_BUILD_TYPE](https://cmake.org/cmake/help/v3.0/variable/CMAKE_BUILD_TYPE.html) variable like: `-DCMAKE_BUILD_TYPE=RelWithDebInfo`

### sanitized build

Sanitized builds include the `-fsanitize` option and produce special binaries. This option accepts one or more named [sanitizers](#sanitizers) that instrument your code to help catch problems at runtime.

The sanitizers are designed to be efficient and generally low overhead compared to other tools for catching C++ bugs. This makes them viable to run in production or staging environments to catch bugs that:

-   might only occur under high load
-   might never occur in unit tests due to lacking coverage or absence of memory or cpu pressure
-   might never occur if the binaries were [debug builds](#debug-build) because [debug builds](#debug-build) slow down execution so much that race conditions or undefined behavior may vanish.

For unit tests a sanitized build should likely be a [debug build](#debug-build) with sanitizers enabled. This way invalid code execution will stop abruptly and present a detailed stack trace of the problem, for quick fixing.

For production a sanitized should be a [profiling build](#profiling-build) with sanitizers enabled such that the binary still runs fast and therefore more closely emulates a [release build](#release-build) that is running in production.

### sanitizers

Sanitizers provide runtime checks for various forms of undefined or suspicious behavior. When enabled they make C++ more akin to other ["safe" languages that trap errors as they happen](http://blog.regehr.org/archives/213).

They are available as part of clang++: <https://clang.llvm.org/docs/UsersManual.html#controlling-code-generation>

### profiling build

Like a [debuggable release build](#debuggable-release-build), a profiling build is a hybrid between a [release build](#release-build) and a [debug build](#debug-build). A profiling build tries to address the [problem of profiling and optimization levels](#problem-of-profiling-and-optimization-levels).

So, a profiling build generally should:

-   Use the [highest compiler optimization level](#compiler-optimization-level)
-   Disable `assert`s by enabling [DNDEBUG](#DNDEBUG)
-   Disable key compiler optimizations or options that make [callstacks](#callstack) more detailed:
    -   Add `-fno-omit-frame-pointer` (no significant [performance](#performance) cost)
    -   Consider adding `-fno-inline-functions` (potentially significant [performance](#performance) cost, so test before adding this to an -O3 build)
-   Enable [debug symbols](#debug-symbols) by passing `-g`. Except when binary size is important and you want to keep it at a minimum the ideal solution is to only request the compiler to add debug metadata need for better [callstacks](#callstack). (This can be done when building with `clang++` by passing `-gline-tables-only` instead of `-g`)

This boils down to:

```cpp
clang++ -O3 -DNDEBUG -fno-omit-frame-pointer -gline-tables-only ...
```

### problem of debugging release-crashes

When production binaries crash the [callstacks](#callstack) in the [backtraces](#backtrace) will not likely contain line numbers for where, exactly, the code crashed. Instead the only clue for what happened is the name of last function called (that was not inlined by the compiler). If the function is simple and only a few lines of code, perhaps this will give you the programmer enough of the lead to see the potential problem and fix the bug that caused the crash. But more likely the function will be composed of many lines of code and/or it may call other functions that have been inlined away.

So really you want the line number for where the crash happened. An ideal solution might be to run the [debug builds](#debug-build), replicate the crash, to get a more detailed backtrace. But more often than not it is difficult to impossible to replicate a crash with [debug builds](#debug-build). An example of this would be a rare race condition that only happens under load: it can't be replicated with a [debug build](#debug-build) because the code runs too slow. Or it can only be replicated under production load and [debug builds](#debug-build) can't be pushed into production since it would hurt [performance](#performance) too much.

The solution to this problem then is to build your [release builds](#release-build) with just enough debug information to get line numbers, without hurting [performance](#performance). For details on how to do this see [debuggable release build](#debuggable-release-build).

### problem of profiling and compiler optimization levels

[Profiling](#profiling) is a great strategy for learning why and where a program is slow. But caution must be used in interpreting the results when profiling because:

-   If you are profiling [release builds](#release-build) then your profiling output is likely not showing inline functions which may mislead you about where time is spent in the program. Profilers generally aggregate timing results in a [calltree](#calltree) composed of multiple times sampling the [callstack](#callstack). If the [callstack](#callstack) is not showing you all the functions because some have been inlined away, you may not be able to see the whole picture.

-   If you are profiling [debug builds](#debug-build) then you should be able to see the entire [callstack](#callstack) and therefore [calltree](#calltree) should be very detailed. However you can't trust the timing results because they represent how much time was spent for unoptimized code.

There is no perfect solution, other than only relying on profiling output to guide your understanding of a program and not letting it be the end-all word. But consider profiling a [profile build](#profile-build) for a binary that should give more trustworthy results than a debug builds](#debug-build) and more detailed [callstacks](#callstack) than a [release builds](#release-build).

## Debugging and Profiling

### Profiling

Profiling is a way to analyze or measure the [efficiency] of a program: the work it is doing when run. Profiling can help measure things like memory usage, how often a particular function is called within a process, or how many threads are running. It is especially useful for determining how to best optimize a program.

Check out [this guide](https://github.com/springmeyer/profiling-guide) for more information on how to profile.

### debug symbols

Extra stuff the compiler encodes in a binary to help [debuggers](#debugger) inspect details about a running or crashed program and [tracers](#tracer) collect detailed information about program execution. Normally to enable debug [symbols](#symbol) you pass `-g` to a compiler. This can be done in either a [release build](#release-build) or a [debug build](#debug-build).

### Debugger

A program like `gdb` or `lldb` that is able to run or "hook" into executables, pause them, inspect variables, print backtraces, and see what threads are doing.

### Tracer

A program like `perf` or `Activity Monitor` that is able to observe program behavior and generate summary reports.

### core file

Core files, also known as core dumps, are a file generated by the system after a program [crash](#crash). The system however must be ask, before a crash happens, to enable them. This is usually done by calling `ulimit -c unlimited` inside a shell. But where is this file saved and how can you locate them? It depends on how the system is configured. The way to find out is to look at the system's [core_pattern](#core_pattern).

Core files contain a snapshot of information about the program at the moment before the program exited and at the time of the crash. Core files can be read by [debugger](#debugger). A common use of [debugger](#debugger) is to generate a [backtrace](#backtrace) from a [core file](#core-file).

More info at <https://en.wikipedia.org/wiki/Core_dump>

### callstack

The `callstack` is the list of functions that are being called at a given moment, as reported in a backtrace. Each thread in a backtrace will have its own `callstack`. Usually a `callstack` from a [debugger](#debugger) is read from bottom to top: the function at the bottom was called first (the parent) and the function at the top was called last (final child).

See also [calltree](#calltree)

### calltree

When sampling a program over time multiple pathways through function calls will be observed. While a [calltree](#calltree) captures what functions are being called at a given moment, a `calltree` is one way to represent a summary of the branches of code that executed over a given time interval. This is a common display format used by [tracers](#tracer).

### backtrace

A backtrace, also known as a stack trace, "is a report of the active stack frames at a certain point in time during the execution of a program". (from <https://en.wikipedia.org/wiki/Stack_trace>).

The C/C++ language has a method called [backtrace and backtrace_symbols](http://man7.org/linux/man-pages/man3/backtrace_symbols.3.html) that can be used to print out the active stack frames. However, because C/C++ crashes might result from invalid use of memory, or out of memory conditions, it is dangerous to try to run more code, even these functions, after a [crash](#crash). Therefore the convention to get backtraces for crashes is to enable [core file](#core-file) generation and use a [debugger](#debugger) to generate a backtrace after the [crash](#crash) has happened.

Have a look at [logbt](https://github.com/mapbox/logbt) for a project that makes generating backtraces easier, on both Linux and OS X.

### core_pattern

The core pattern describes where the system should create a [core file](#core-file). Therefore it usually consists of a path on the filesystem and one or more tokens that are populated to dynamically construct the filename.

On Linux, the default core_pattern can be read by doing:

```bash
cat /proc/sys/kernel/core_pattern
```

More info at <http://man7.org/linux/man-pages/man5/core.5.html>

And on OS X, the default can be read by doing:

```bash
sysctl -n kern.corefile
```

More info at <https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man5/core.5.html>

### undefined behavior

See <http://blog.regehr.org/archives/213> and <http://blog.llvm.org/2011/05/what-every-c-programmer-should-know.html>

### static analysis

Static analysis of code is checks or assertions that are done on the code without needing to execute or run the code. The advantage of this is that these checks may find bugs which [runtime analysis](#runtime-analysis) alone would not see. Ideally you implement both methods in your project.

### runtime analysis

Runtime analysis of code is checks or assertions in the code that happen when your code runs. Usually these checks, like the [santizers](#sanitizers), will only find problems in code that has tests or that is actually used. To find problems in code that you don't have tests for, consider [static analysis](#static-analysis). Ideally you implement both methods in your project.

## Memory concepts

### allocation

A term used to describe when memory is requested to create space to hold a variable. This request is answered by the [allocator](#allocator) for [dynamically allocated memory](#allocator). Usually we are talking about [dynamically allocated memory](#allocator) when we speak about allocation, unless the object was allocated on the [stack](#stack-allocation). The [dynamic memory allocator](#allocator) is often the bottleneck for programs that have been optimized in all the obvious ways. In other words, once you have optimized the easy things and looked at what still takes time in a program, a likely culprit is allocation. Therefore the key to unlocking the highest levels of [efficiency](#efficiency) is often reducing allocations.

### deallocation

A term used to describe when dynamically allocated memory is released. This is when the space previously allocated to hold a variable is let go such that it can be used for allocating new variables. This release is answered by the [allocator](#allocator), and can take the same amount of time to service as [allocation](#allocation).

### reallocation

A term used to describe when the space previously allocated to hold a variable is discarded and re-created at a new size, usually larger. Reallocation is expensive because it triggers both [deallocation](#deallocation) and [allocation](#allocation).

### instantiate

To instantiate a C++ type is to create a variable that refers to an instance of that type. That instance may point to [dynamically allocated memory](#allocator) on the [heap](#heap) if the [new keyword](#new-keyword) was used. Or if the [new keyword](#new-keyword) was not used then [stack allocation](#stack-allocation) is how the instance was created and the object will be temporary and go out of scope.

For example, to instantiate a temporary variable `a` that is type `std::int64_t` and value `1`, on the stack, you would do:

```c++
{
  std::int64_t a = 1;
}
```

After program control passes `}` the the variable has gone out of scope and will no longer exist.

If you instantiate the same type but with the [new keyword](#new-keyword), then:

-   the type returned will be a pointer to the type rather than just the type
-   the pointer will point to a dynamically allocated [memory address](#memory-address)
-   the pointer must be deleted to avoid a memory leak

Here is an example of dynamical allocation:

```c++
{
  std::int64_t * a = new std::int64_t(1);
  delete a;
}
```

Note: when instantiating classes without the [new keyword](#new-keyword) you might think that only [stack allocation](#stack-allocation) is being used. For example when creating a `std::string` like:

```c++
std::string a("hello");
```

That is using [stack allocation](#stack-allocation) so you might thing that no dynamic memory is being allocated. However, dynamic memory is being allocated. This is because `std::string` is a container class that holds an arbitrary length array of characters inside. So to create space for this arbitrary array of characters it uses [dynamically allocated memory](#allocator) internally. It also cleans up this memory automatically by [deallocating](#deallcation) when the `std::string` goes out of scope. Both allocation and deallocation take time. So when [performance](#performance) is critical and you want to avoid dynamic allocations, always give consideration to how the class is implemented that you are instantiating.

Note: We say above that `std::string` uses [dynamically allocated memory](#allocator) internally. This is true except when the `std::string` implementation itself has been optimized to reduce allocations. Many implementations are clever enough and care about [performance](#performance) enough to do this using an optimization internally that uses [stack allocation](#stack-allocation) for the array of characters. This only works for small strings and as such is called the [short/small size optimization](#small-size-optimization) or SSO.

### memory address

A value like `0x7fb20ac02680` that points to a location in the program's [address space](#address-space). Various things are "Addressable" in this space. See <https://en.wikipedia.org/wiki/Memory_address#Address_space_in_application_programming> for more details.

### address space

In C++ the term address space usually refers to the scope of memory the program has access to. You can refer to something in code by referencing its [memory address](#memory-address) if it is in the same address space as your code.

### free store

A term use to describe free memory or [address space](#address-space) not yet requested.

### new keyword

In C++ the `new` keyword is used to allocate an object dynamically, on the [heap](#heap). The result is a pointer to a class instance. This object must be cleaned up by calling `delete` on the pointer.

### allocator

C and C++ programs all depend upon a single dynamic memory allocator to implement dynamic management functions like `malloc` and related functions (`free`, `calloc`, and `realloc`).

We call this "dynamic" memory because the size of memory requested is determined at runtime by your C++ code: what objects it creates and how much space they require.

Dynamic memory allocation requires able [RAM](https://en.wikipedia.org/wiki/Random-access_memory) available on your machine.

The default allocator is usually tuned to perform well under varied conditions and provided as part of the implementation of the C library. [Custom allocators](#custom-allocator) can be swapped in and may perform better.

### stack

In C++ when you write code like:

```c++
int num = 1;
MyClass klass = MyClass();
```

You are depending on pre-allocated [address space](#address-space) reserved for your program for placing those temporary objects.

This pre-allocated [address space](#address-space) is the stack. Your program has access to it automatically. It is local to a thread of execution.

When a program runs out of stack space (too much is allocated on the stack) you get a [stack overflow](https://en.wikipedia.org/wiki/Stack_overflow).

Note: there is also a `std::stack` in the C++ STL. This is different - this is a [data structure](http://en.cppreference.com/w/cpp/container/stack).

### stack allocation

When an object is created without the `new` keyword, it is allocated on the [stack](#stack) and does not need to be manually deleted.

Generally stack allocation is faster than [heap allocation](#heap-allocation) because it does not require a call to the [dynamic memory allocator](#allocator).

Often very tuned C++ programs find their remaining bottlenecks are memory allocation. In this case, to further increase [performance](#performance), developers often look for ways to utilize stack allocation without the fear of [overflow](https://en.wikipedia.org/wiki/Stack_overflow). See [stack alloc](https://howardhinnant.github.io/stack_alloc.html) as well as "SmallVector" inside llvm which both allocate on the stack up for a limite number of elements. See [this talk](https://youtu.be/vElZc6zSIXM?t=135) or [this stack overflow (pun not intended) mention here](https://stackoverflow.com/a/42122275).

Read more [here](https://stackoverflow.com/a/80113)

### heap allocation

When an object is created with the `new` keyword  it is allocated on the [heap](#heap) and must be deleted otherwise your program will have a memory leak.

When a chunk of memory is initialized by calling `malloc`, this is also heap allocation. The `new` keyword used with a custom object calls `malloc` under the hood with the right arguments to request memory for the custom object.

The advantage of heap allocation is:

-   The stack is limited in size and might [overflow](https://en.wikipedia.org/wiki/Stack_overflow)
-   The result of heap allocation is a pointer that can be passed around and is not limited to the local scope.

Note: as of C++11, now that you can move stack allocated objects using `std::move` you can treat these objects more like heap allocated pointers (they can be passed around and, once moved, are not limited to the local scope).

Read more [here](https://stackoverflow.com/a/80113)

### heap

The heap is [address space](#addres-space) in RAM.

### custom allocator

A custom allocator may be able to allocate memory faster, or use memory more efficiently. An example of such an allocator is [jemalloc](https://github.com/jemalloc/jemalloc).

### memory locality

A term used to describe when the location of data ([memory address](#memory-address)) is coincident enough (often because layout is contiguous) such that repeated access of that data results in a high cpu cache hit ratio.

Also known as [`locality of reference`](https://en.wikipedia.org/wiki/Locality_of_reference).

See <https://deplinenoise.wordpress.com/2013/12/28/optimizable-code> for an example of how memory locality relates to performance.

## Language concepts and keywords

### inline keyword

A standalone function or a class method can be marked with the `inline` keyword like:

```c++
inline int get() {
    return 1;
}
```

This asks the compiler to [inline-expand](#inline-expands) your code.

In addition the `inline` keyword has to be used for [definitions](#definition) in headers to not violate the [One-Definition Rule (ODR)](#one-definition-rule):

Learn more at <https://isocpp.org/wiki/faq/inline-functions#inline-member-fns>

### one-definition rule

The `inline` keyword has to be used for [definitions](#definition) in headers to not violate the One-Definition Rule (ODR):

```c++
// myheader.hpp

inline int get() {
    return 1;
}

template <typename T> int put(int x) {
    return x + 1;
}
```

The definition for `get` in the header needs `inline` - not for [performance](#performance) reasons - but to make sure multiple translation units do not clash exposing the same definition.

Except for fully specialized templates (`template <>`) templates are `inline` by default and do not require the `inline` keyword when defining them in headers.
It's a good practice to just always specify the `inline` keyword for definitions in headers.

Learn more at <http://en.cppreference.com/w/cpp/language/definition#One_Definition_Rule>

### inline-expands

Also know as "inlined", this describes when a compiler moves code such that "the function’s code gets inserted into the caller’s code stream" from <https://isocpp.org/wiki/faq/inline-functions#overview-inline-fns>

Inlining functions allows the compiler's optimizer to "see" across [translation unit](#translation-unit) boundaries and optimize the code in its larger context.

This expansion happens during compilation so the impact of inlining is on the resultant [object files](#object-files) and binaries produced. The compiler does not rewrite [source code](#source-code).

However, let's use [source code](#source-code) as a simple example of what inlining is about.

Given the following code:

```c++
inline int get() {
    return 1;
}

int main() {
    int result = get();
    return result;
}
```

The inline-expanded version would likely be seen/re-written internally by the compiler to be more like:

```c++
int main() {
    int result = 1;
    return result;
}
```

What the compiler does is hard to predict (its job is to optimize code in innovative ways!), so this is an incomplete example, but hints at the start of what may be happening behind the scenes.
You can use Clang's [`-Rpass`](https://clang.llvm.org/docs/UsersManual.html#options-to-emit-optimization-reports) switch to inspect what the inliner is doing and especially where it fails to inline.

You could also imagine the compiler taking this example a step further and doing:

```c++
int main() {
    return 1;
}
```

Because in our example it knows that `result` will always be `1`.

### inlined

When a compiler [inline-expands](#inline-expands) a function.

### declaration

A declaration represents signatures but not the actual implementation.

A declaration for a function looks like:

```c++
int get();
```

A declaration for a class looks like:

```c++
class C;
```

A class declaration (also called [forward declaration](#forward-declaration)) is an [incomplete type](http://en.cppreference.com/w/cpp/language/type#Incomplete_type).

The primary use-case for incomplete types is [forward declaring](#forward-declaration) classes in header files to speed up compilation by hiding implementation details from users including the header.

In contrast to a declaration a [definition](#definition) consists of the implementation.

Lean more at <http://en.cppreference.com/w/cpp/language/declarations>

### definition

A definition is code that describes the types an object like a function, class, or template.

A definition for a function looks like:

```c++
int get() {
  return 1;
}
```

Each definition is a [declaration](#declaration).

When code is designed to be a [precompiled library](#precompiled-library) the definition is often included on its own in a header file (.hpp) and the [implementation](#implementation) is put in a `.cpp` file.

It is also valid to have the entire [implementation](#implementation) in the header file. When only this is done then the library is called a [header-only library](#header-only-library)

Learn more at:

-   <http://www.goldsborough.me/c/c++/linker/2016/03/30/19-34-25-internal_and_external_linkage_in_c++/#declaration-vs-definition>
-   <http://en.cppreference.com/w/cpp/language/definition>

### Forward declaration

Refers to when you provide a [declaration](#declaration) of a [symbol](#symbol) without a definition.

Learn more at:

-   <http://www.goldsborough.me/c/c++/linker/2016/03/30/19-34-25-internal_and_external_linkage_in_c++/#declaration-vs-definition>

### implementation

An implementation is the code for a function inside the `{ ... }`. So, for example, an implementation of a function looks like:

```c++
int get() {
    return 1;
}
```

When the implementation is in a .cpp file it will be compiled into an [executable](#executable) or a [precompiled library](#precompiled-library).

When the implementation is in a .hpp file completely it is considered a [header-only library](#header-only-library).

Read more at <https://stackoverflow.com/a/1410632>

### pointer

<http://en.cppreference.com/w/cpp/language/reference>.

### reference

A reference in C++ is denoted by the `&` keyword when applied to a variable. When you dereference a [pointer](#pointer) you get a reference. Also when you pass an object "by reference" you are asking the object to not be copied. When you pass an object "by value" you are asking for the object to be copied.

Learn more at <http://en.cppreference.com/w/cpp/language/reference>.

### noexcept

See <http://en.cppreference.com/w/cpp/keyword/noexcept> 

### bytes

<https://en.wikipedia.org/wiki/Byte>
<http://en.cppreference.com/w/cpp/types/byte> [c++17]

### bits

<https://en.wikipedia.org/wiki/Bit> 

### structure packing

Learn more at <http://www.catb.org/esr/structure-packing>

### sizeof

Function built-in to C++ that can display the size, in [bytes](#bytes) of a type.
<http://en.cppreference.com/w/cpp/language/sizeof>

### memory alignment

[C++ objects and alignment](http://en.cppreference.com/w/c/language/object)

See also <https://jonasdevlieghere.com/order-your-members/#alignmentpadding>

### statically typed

When a type is enforced at compile time.

Statically typed languages allows us to be much more descriptive. An example is [strong types](#strong-type).

### dynamically typed

Languages is dynamically typed (or just  dynamic) when type checking happens at run time. Example languages: Python, JavaScript.

### public member

<http://en.cppreference.com/w/cpp/language/access>

### private member

<http://en.cppreference.com/w/cpp/language/access> 

### protected member

<http://en.cppreference.com/w/cpp/language/access>

### class

Same as a [struct](#struct) except all members are default private.

### struct

Same as a [class](#class) except all members are default public.

### assembly

An ascii output able to be produced by a [compiler](#compiler). It is fully optimized and can be 1-to-1 translated to [machine code](#machine-code). It can be viewed to understand what compiler optimizations were made.

### machine code

<https://en.wikipedia.org/wiki/Machine_code>

### implicit conversion

When a type is automatically converted to another type. This is often [a cause of bugs](https://github.com/mapbox/mapbox-gl-native/pull/5754) and a surprise to developers.

To prevent this behavior use the [explicit keyword](#explicit).

<http://en.cppreference.com/w/cpp/language/implicit_conversion>

### explicit

<http://en.cppreference.com/w/cpp/language/explicit>

### explicit conversion

<http://en.cppreference.com/w/cpp/language/explicit_cast>

### strong type

Creating a custom type to enforce correct usage of a function or class.

When we write in a compiled language, like C++, our main target audience is the compiler. This is a very important concept to bear in mind. Second most important is fellow developers trying to understand what you have written years later and users downstream. If we can work well with the compiler, we can serve all audiences better.

### deterministic

Definition of deterministic in English: 

"Relating to the philosophical doctrine that all events, including human action, are ultimately determined by causes regarded as external to the will.
‘a deterministic theory’"

In computer science, a deterministic algorithm is an algorithm which, given a particular input, will always produce the same output, with the underlying machine always passing through the same sequence of states.

<https://en.wikipedia.org/wiki/Deterministic_algorithm>

### fits into register

Okay to pass by value.

### constexpr

The constexpr specifier declares that it is possible to evaluate the value of the function or variable at compile time.

### singleton

A singleton is a instance of something that is created only once. When accessed the same instance is always returned.

There are a variety of ways to implement a singleton. None of them are recommended unless you know what you are doing.

Learn more [here](https://en.wikipedia.org/wiki/Singleton_pattern).

### static initialization

Read about this at <http://en.cppreference.com/w/cpp/language/initialization>.

### global static

A global static is a variable created in the global scope that points to [static data](#static-data).

Generally it is bad practice to create global statics due to the [possibility of the static initialization fiasco](https://isocpp.org/wiki/faq/ctors#static-init-order), so only do this if you know what you are doing.

They can be created like:

```c++
#include <iostream>

static int one = 1;

int main() {
    std::cout << one << "\n";    
}
```

### static member

A static member is [static data](#static-data) declared on a class that uses the `static` keyword. For example:

    #include <iostream>
    #include <string>

    class HelloWorld {
     public:
        static int one_;
    };

    int HelloWorld::one_ = 1;

    int main()
    {
      std::cout << "One: " << HelloWorld::one_ << "!\n";
    }

Using the `static` keyword on a class member allows the member to be accessed without creating an instance of that class. So, that member is not "bound" to a class instance.

Read more at <http://en.cppreference.com/w/cpp/language/static>.

### static method

A static method is a function defined on a class that uses the `static` keyword. For example:

```cpp
#include <iostream>
#include <string>

class HelloWorld {
 public:
    static std::string hello() {
        return "world";
    }
};

int main()
{
  std::cout << "Hello " << HelloWorld::hello() << "!\n";
}
```

Using the `static` keyword on a class method allows the method to be called without creating an instance of that class. So, that method is not "bound" to a class instance.

Read more at <http://en.cppreference.com/w/cpp/language/static>.

### static data

Data created statically. In short this means a variable created with the static keyword like:

```c++
static int one = 1;
```

The longer explanation is that it is data created by [static initialization](#static-initialization). This basically means that the variable is created at the time the program starts and therefore is available for the whole time the program is running (aka "static storage duration").

Often static is used in combination with [constexpr](#constexpr) to ask for a variable to have static storage duration and have its value computed at compile time.

### macro

<https://gcc.gnu.org/onlinedocs/cpp/Macros.html>

## Node.js & C++

### Node

Node.js is a JavaScript runtime built on Chrome's [V8](#v8) JavaScript engine.

It is a command line tool that can execute Javascript files and C++ code compiled as [a node addon](#node-addon).

Node builds on top of [V8](#v8) by providing ways to do evented, non-blocking [I/O](#io). What this means in practice is that node runs your code on an [event loop](#event-loop) and provides an API for deferring work through asynchronous function callbacks which execute in a [threadpool](#threadpool). This "work" might be traditional [I/O](#io) like accessing the filesystem or a data on a network, or it could be any kind of intensive computations.

This functionality in node is internally using a C library called [libuv](#libuv).

### V8

V8 is a Javascript "engine", or a Javascript interpreter. It translates Javascript into more efficient machine code (native assembly code), then executes it. V8 gives developers access to functionality (networking, DOM handling, external events, HTML5 video, canvas and data storage) needed to control the web browser. V8 does not allow access to the filesystem (or provide other features you'd normally associate with a scripting language used on the serverside). Therefore [Node](#node) implements features like filesystem access on top of V8.

V8 is [open source and written in C++](https://github.com/v8/v8).

### Node Addon

Node provides some "addons" built in, like the `zlib` module, which is implemented [in C++ inside node core](https://github.com/nodejs/node/blob/df63e534584a54dcf02b37446e1e821382e3cef3/src/node_zlib.cc).

You can also write your own addon and include it dynamically within your application.

See [node addon docs for more details](https://github.com/mapbox/cpp/blob/master/node-cpp.md)

### Event loop

The event loop is what allows Node.js to perform non-blocking [I/O](#io) operations — despite the fact that JavaScript is single-threaded — by offloading operations to the system kernel whenever possible.

The event loop runs in the main process (or main thread) and responds to an event queue: grabs the top item in the event queue, executes it, and then grabs the next item.

Learn more at <https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick>

### Threadpool

In Node.js this is a queue of threads - of fixed size - that are launched at started and ready to execute work.

To learn how to control the threadpool size with `UV_THREADPOOL_SIZE` and much more see the [node addon docs](https://github.com/mapbox/cpp/blob/master/node-cpp.md)

### libuv

A library that provides a cross-platform API for asynchronous [I/O](#io). Most importantly libuv provides access, for [node addons](#node-addon), to the [Node Threadpool](#threadpool).

Libuv stands for "lib-ultra-event" and [is open source and written in C](http://libuv.org)

See [node addon docs for more details](https://github.com/mapbox/cpp/blob/master/node-cpp.md)
