# Building C++

#### development toolchain

The toolset used to generate C++ code into [executables](#executable) for a computer. Typically consists of a [compiler](#compiler) and [linker](#linker). 

All C++ code falls into the ["ahead-of-time" compilation](https://en.wikipedia.org/wiki/Ahead-of-time_compilation) category, which means code is compiled and linked separately from execution, unlike  ["just-in-time"](https://en.wikipedia.org/wiki/Just-in-time_compilation) compilation languages, like JavaScript.

#### compiler

A program that converts a C++ source code into machine code (binary) files readable by a computer. Compilers turn `.cpp` code into `.o` [object files](#object-file). 

Example open source compilers are [clang](#clang) or [gcc](#gcc).

#### linker

A program that combines object files into a single [executable](#executable) or [library](#library). Typically a step enacted by the [compiler](#compiler).

#### object file

An object file contains symbols, compiled code, external symbols, and other static data. If you are compiling three C++ files – `one.cpp`, `two.cpp`, `three.cpp` – they will be compiled into three object files – `one.o`, `two.o`, `three.o`. These can subsequently be combined by a [linker](#linker) and turned into a [library](#library) or [executable](#executable).

#### executable

A binary file and/or program that can be run by a computer. This is the outcome of [compiling](#compiler) and [linking](#linker). Specifically, an executable has a `main()` function or _entry point_. 

#### posix

A set of standards for maintaining compatability between unix-like operating systems. Generally posix is synonymous with unix when it comes to what system APIs exist in C/C++ code to interface with the operating system. With minor exceptions POSIX APIs work the same on linux and osx.

#### library

A set of reusable C++ code that can be shared across projects. Libraries can be organized very differently, but typically contain a set of header files (static library) or pre-compiled binaries (dynamic/shared library). 

Opposite of an [executable](#executable), a library does not have a `main()` entry point and, instead, provide a set of functions and classes that can be called by other C/C++ libraries or executables. It must include at least a single [header file](#header) so the _calling application_ knows the definition of the interfaces provided. A library can be as static archives ([static library](#static-library)) or as a [shared library](#dynamicshared-library).

#### dynamic/shared library

A shared library is only required at runtime, meaning it has already been [compiled](#compiler) into machine-readable code and is just required alongside your code when it executes.

#### static library

A static library is not required at runtime. Instead when a precompiled static library is linked by a calling application all of its object code is linked into that calling applications executable. This step is often called "static linking". The source code of a static library looks identical to a shared library (both contain headers and .cpp files). The only difference is the way it is built.

#### header

A file with the `.hpp` or `.h` file extension.

#### header-only library: when all of the source code is in the .hpp file (none in the .cpp)
#### precompiled library: when source code is in .cpp files that is compiled and linked into a shared (aka dymamic) library (.so on linux, .dylib on os x)
#### when to write compiled library vs a header only library?
**hide dependencies that are only needed at runtime and not at compile time by users of library**
**when you want to ship binary code that you don't want to recompile or re-link**
#### c++11: https://isocpp.org/wiki/faq/cpp11-language
#### c++14: https://isocpp.org/wiki/faq/cpp14-language
#### c++1y
#### c++98
#### c++11 move semantics (std::move)
#### LLVM
#### gcc
#### clang
#### g++
#### clang++
#### versioned symbols
#### libc
#### glibc
#### libstdc++
#### libc++
#### undefined behavior
#### versioned symbols
#### abi compatibility

# Writing C++

#### template
#### macro
#### mutex
#### const

# Node.js & C++

#### Node
#### V8
#### event loop
#### libuv
#### threadpool
#### worker
#### C++ bindings
#### POSIX
#### non-blocking/blocking
#### [NAN](https://github.com/mapbox/cpp/blob/node-addons/node-cpp.md#nodejs-c-addons)

# FAQ

#### Can you mix binaries between compilers, like g++ and clang++
#### Can you mix binaries between standards, like c++11 and c++14
#### How is C/C++ used in operating systems like Linux and OS X