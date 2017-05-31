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

A set of reusable C++ code that can be shared across projects. Libraries can be organized very differently, but typically contain a set of header files (static library) or [pre-compiled binaries (dynamic/shared library)](#precompiled-library).

Opposite of an [executable](#executable), a library does not have a `main()` entry point and, instead, provide a set of functions and classes that can be called by other C/C++ libraries or executables. It must include at least a single [header file](#header) so the _calling application_ knows the definition of the interfaces provided. A library can be as static archives ([static library](#static-library)) or as a [shared library](#dynamicshared-library).

#### precompiled library

A library that contains source code in .cpp files that is compiled and linked into a [shared library](#shared-library) or [static library](#static-library)

#### shared library

A shared library is a type of [precompiled library](#precompiled-library). It required at link time and runtime by applications that depend on it. This means it has already been [compiled](#compiler) into machine-readable code and is just required alongside your code when it executes.

Also known as a dynamic library.

#### static library

A static library is a type of [precompiled library](#precompiled-library). It is only required at link time. When a static library is linked by a calling application all of its object code is put into that calling applications executable. This step is often called "static linking". The source code of a static library looks identical to a shared library (both contain headers and .cpp files). The only difference is the way it is built.

#### header

A file with the `.hpp` or `.h` file extension.

#### header-only library

Used to describe when code is organized such that all of the source code is in the .hpp file such that:

 - Not cpp files need to be compiled
 - To use the library, no library needs to be linked (just `#include <the header>` is enough

#### performant

A very dubious word, usually meant to refer to a program that performs well enough by some measure of enough. Prefer describing a program as either having acceptable [performance](#performance) or acceptable [efficiency](#efficiency).

#### performance

How quickly a program does its work. Improving performance involves doing work faster. How much performance can be increased is a function of how long certain operations take on a computer: https://gist.github.com/jboner/2841832.

#### efficiency

How much work is required by a task. Improving efficiency requires doing less work. An efficient program is one which does the minimum (that we're aware of) amount of work to accomplish a given task.

- See also: CppCon 2014: Chandler Carruth "Efficiency with Algorithms, Performance with Data Structures" https://youtu.be/fHNmRkzxHWs?t=754

#### optimization technique

Changing code to increase [efficiency](#efficiency) or [performance](#performance).

#### memoization

An [optimization technique](#optimization-technique) that reduces the amount of work needed and increases [efficiency](#efficiency) by storing the results of expensive function calls or data access.

#### compiler optimization level

[Compilers](#compiler) transform code into binaries that can run on various platforms. But they also optimize that code in specific ways depending on the `optimization level`.

The `optimization level` impacts both how fast the resulting binaries will run, their size, and how much diagnostic information we can learn about the running binary through profiling.

The most important reason to understand `optimization levels` is to understand the larger concept of [build modes](#build-modes) including:

  - [release builds](#release-build)
  - [debuggable release builds](#release-with-debug-build)
  - [debug builds](#debug-build)
  - [profiling builds](#profiling-build)
  - [sanitized builds](#sanitized-build)

There are hundreds of different optimization options inside compilers. Luckily we don't need to know about the details because the common open source compilers (`clang++` and `g++`) provide a simplified "O" interface. You simply pass `-ON` where `O` is a capitol O and `N` is a number between `0` and `3`.


 - `-O0` - "oh zero" disables optimization.
   - The program will run very very slowly and the resulting binary will likely be much larger.
   - The advantage is that [backtraces](#backtrace) to be much more detailed because inlining and other optimizations are disabled that would otherwise shrink the size of the [callstack](#callstack).
   - A [backtrace](#backtrace) for a crash will likely show the exact line number where a SIGSEGV or SIGABRT happened.

 - `O1` - "oh one" is moderately optimized.
   - Not all functions will be inlined, but the program should still run reasonably fast.
   - This level is a good compromise when you want good [backtraces](#backtrace) but also not awful speed.

 - `O3` - "oh three" enables most all optimizations inside the compiler.
   - The program will run as fast as possible and the resulting binary will be smaller (except where inlining might increase its size somewhat).
   - Backtraces will likely be much less detailed and [callstacks](#callstack) will only have minimal usefulness for knowing the location of a crash (no line numbers).

Compilers are rapidly adding more and more optimizations and shifting around which internal optimizations are enabled in which "O" level. See also:

 - http://llvm.org/docs/Passes.html for a listing of all the internal compiler optimizations in clang++ which you don't need to know specifically, but which are under-the-hood of the "O" levels.
 - http://stackoverflow.com/a/15548189 for a detailed summary of which internal optimizations are grouped in which "O" level across clang++ releases.
 - https://gcc.gnu.org/onlinedocs/gcc/Optimize-Options.html for g++ specific docs.


#### DNDEBUG

`DNDEBUG` stands for `do not debug` or `define no debug` and is a [preprocessor macro](#macro) or `#define`. It is passed as an option to a [compiler](#compiler).

To define it pass `-DNDEBUG` to the compiler. It is the opposite of [`-DDEBUG`](#DDEBUG) which stands for `yes, please, do debug`.

The `-DNDEBUG` flag internally tells the compiler to remove [assert](http://en.cppreference.com/w/cpp/error/assert)s from the code. This allows the program to run faster (less expensive checks) at the cost of no longer preventing the program from stopping on `assert`s that might protect from [undefined behavior](#undefined-behavior).

:warning: Do not be tempted to write unit tests using `assert` unless you are 100% confident your build system sets `-DDEBUG`. Otherwise your tests will not be testing what you think because the `assert`s may be gone!

Also, sometimes programs hook into the `DNDEBUG` `#define` to apply their own custom behavior. For example `protozero` changes behavior depending if `-DNDEBUG` is set. If it is set then C++ exception are thrown only when something terrible goes wrong parsing. If `-DNDEBUG` is not set then `protozero` assumes you want `-DDEBUG` and enables `asserts` that help catch bugs that might not ever show up with `-DNDEBUG` (aka [release mode](#release-mode)). More details on this example at https://github.com/mapbox/protozero/blob/master/doc/tutorial.md#asserts-and-exceptions-in-the-protozero-library.

See also: http://en.cppreference.com/w/cpp/error/assert

#### DDEBUG

`DDEBUG` stands for `yes, please, do debug` or `define debug` and is a [preprocessor macro](#macro) or `#define`. It is passed as an option to a [compiler](#compiler).

To define it pass `-DDEBUG` to the compiler. It is the opposite of [`-DDEBUG`](#DDEBUG) which stands for `yes, please, do debug`.

When defined [assert](http://en.cppreference.com/w/cpp/error/assert) in the code will be kept and enabled.

#### release mode

Release mode is when a [release build](#release-build) is run.

#### release build

A release build describes a C++ binary built with a high [compiler optimization level](#compiler-optimization-level) and the `-DNDEBUG`flag. A release build is often built without flags enabling [debug symbols](#debug-symbols) (when developers want the smallest possible binaries). But because [debug symbols](#debug-symbols) do not impact program speed, some projects and developers prefer to include flags enabling [debug symbols](#debug-symbols) even in release builds.

#### debug build

A debug build describes a C++ binary built with the [`-DDEBUG`](#DDEBUG) flag and with lower [compiler optimization levels](#compiler-optimization-level). A debug build is also usually built with flags enabling [debug symbols](#debug-symbols)

Debug builds run slower and may assert on hidden bugs in the code. They are very useful for testing and finding problems in the code.

#### debuggable release build

Like a [profiling build](#profiling-build), a debuggable release build is a hybrid between a [release build](#release-build) and a [debug build](#debug-build). A  debuggable release build tries to address the [problem of debugging release-crashes](#problem-of-debugging-release-crashes).

A build like this should:

  - Use the [highest compiler optimization level](#compiler-optimization-level)
  - Enable [debug symbols](#debug-symbols) by passing `-g`.

This is similar to a [profiling build](#profiling-build) but without any extra flags that might hurt performance.

Note: Developers that use cmake can automatically build this way by passing the `RelWithDebInfo` value to the [CMAKE_BUILD_TYPE](https://cmake.org/cmake/help/v3.0/variable/CMAKE_BUILD_TYPE.html)` variable like: `-DCMAKE_BUILD_TYPE=RelWithDebInfo`

#### sanitized build

Sanitized builds are builds that include the `-fsanitize` option. This option accepts one or more named [sanitizers](#sanitizers) that instrument your code to help catch problems at runtime.

The sanitizers are designed to be efficient and generally low overhead compared to other tools for catching C++ bugs. This makes them viable to run in production or staging environments to catch bugs that:

  - might only occur under high load
  - might never occur in unit tests due to lacking coverage or absence of memory or cpu pressure
  - might never occur if the binaries were [debug builds](#debug-build) because [debug builds](#debug-build) slow down execution so much that race conditions or undefined behavior may vanish.

For unit tests a sanitized build should likely be a [debug build](#debug-build) with sanitizers enabled. This way invalid code execution will stop abruptly and present a detailed stack trace of the problem, for quick fixing.

For production a sanitized should be a [profiling build](#profiling-build) with sanitizers enabled such that the binary still runs fast and therefore more closely emulates a [release build](#release-build) that is running in production.

#### sanitizers

Sanitizers provide runtime checks for various forms of undefined or suspicious behavior. When enabled they make C++ more akin to other ["safe" languages that trap errors as they happen](http://blog.regehr.org/archives/213).

They are available as part of clang++: https://clang.llvm.org/docs/UsersManual.html#controlling-code-generation

#### profiling build

Like a [debuggable release build](#release-with-debug-build), a profiling build is a hybrid between a [release build](#release-build) and a [debug build](#debug-build). A profiling build tries to address the [problem of profiling and optimization levels](#problem-of-profiling-and-optimization-levels).

So, a profiling build generally should:

  - Use the [highest compiler optimization level](#compiler-optimization-level)
  - Disable `assert`s by enabling [DNDEBUG](#DNDEBUG)
  - Disable key compiler optimizations or options that make [callstacks](#callstack) more detailed:
    - Add `-fno-omit-frame-pointer` (no significant performance cost)
    - Consider adding `-fno-inline-functions ` (potentially significant performance cost, so test before adding this to an -O3 build)
  - Enable [debug symbols](#debug-symbols) by passing `-g`. Except when binary size is important and you want to keep it at a minimum the ideal solution is to only request the compiler to add debug metadata need for better [callstacks](#callstack). (This can be done when building with `clang++` by passing `-gline-tables-only` instead of `-g`)

This boils down to:


```cpp
clang++ -O3 -DNDEBUG -fno-omit-frame-pointer -gline-tables-only ...
```

#### problem of debugging release-crashes

When production binaries crash the [callstacks](#callstack) in the [backtraces](#backtrace) will not likely contain line numbers for where, exactly, the code crashed. Instead the only clue for what happened is the name of last function called (that was not inlined by the compiler). If the function is simple and only a few lines of code, perhaps this will give you the programmer enough of the lead to see the potential problem and fix the bug that caused the crash. But more likely the function will be composed of many lines of code and/or it may call other functions that have been inlined away.

So really you want the line number for where the crash happened. An ideal solution might be to run the [debug builds](#debug-build), replicate the crash, to get a more detailed backtrace. But more often than not it is difficult to impossible to replicate a crash with [debug builds](#debug-build). An example of this would be a rare race condition that only happens under load: it can't be replicated with a [debug build](#debug-build) because the code runs too slow. Or it can only be replicated under production load and [debug builds](#debug-build) can't be pushed into production since it would hurt performance too much.

The solution to this problem then is to build your [release builds](#release-build) with just enough debug information to get line numbers, without hurting performance. For details on how to do this see [debuggable release build](#release-with-debug-build).


#### problem of profiling and compiler optimization levels

Profiling is a great strategy for learning why and where a program is slow. But caution must be used in interpreting the results when profiling because:

 - If you are profiling [release builds](#release-build) then your profiling output is likely not showing inline functions which may mislead you about where time is spent in the program. Profilers generally aggregate timing results in a [calltree](#calltree) composed of multiple times sampling the [callstack](#callstack). If the [callstack](#callstack) is not showing you all the functions because some have been inlined away, you may not be able to see the whole picture.

 - If you are profiling [debug builds](#debug-build) then you should be able to see the entire [callstack](#callstack) and therefore [calltree](#calltree) should be very detailed. However you can't trust the timing results because they represent how much time was spent for unoptimized code.

There is no perfect solution, other than only relying on profiling output to guide your understanding of a program and not letting it be the end-all word. But consider profiling a [profile build](#profile-build) for a binary that should give more trustworthy results than a debug builds](#debug-build) and more detailed [callstacks](#callstack) than a [release builds](#release-build).

#### signal

Signals are a method of interprocess communication. When a program exits it returns an integer code. That code, if the program crashed, will encode an integer id of a signal. This offers a way for monitoring programs that crashed and why. The signals that result from a [crash](#crash) are listed at http://en.cppreference.com/w/c/program/SIG_types.

To know the exit code of a signal you add the `<signal id> + 128`. You can find the id of each signal at http://www.comptechdoc.org/os/linux/programming/linux_pgsignals.html.

More info at https://en.wikipedia.org/wiki/Unix_signal

#### crash

A crash is a general term to describe when execution of the program exits in an unintended and unrecoverable way. There are a variety of reasons a program may crash, ranging from a bug that created a fatal error condition to another program killing your program. Each crash can be described one of a known set of signals, which also maps to a return code.

For example a segfault (segmentation fault or violation) leads to a `SIGSEGV` signal, which is id `11`, and an exit code of `128+11` == 139.

#### Debugger

A program like `gdb` or `lldb` that is able to run or "hook" into executables, pause them, inspect variables, print backtraces, and see what threads are doing.

##### Tracer

A program like `perf` or `Activity Monitor` that is able to observe program behavior and generate summary reports.

#### core file

Core files, also known as core dumps, are a file generated by the system after a program [crash](#crash). The system however must be ask, before a crash happens, to enable them. This is usually done by calling `ulimit -c unlimited` inside a shell. But where is this file saved and how can you locate them? It depends on how the system is configured. The way to find out is to look at the system's [core_pattern](#core_pattern).

Core files contain a snapshot of information about the program at the moment before the program exited and at the time of the crash. Core files can be read by [debugger](#debugger). A common use of [debugger](#debugger) is to generate a [backtrace](#backtrace) from a [core file](#core-file).

More info at https://en.wikipedia.org/wiki/Core_dump

#### callstack

The `callstack` is the list of functions that are being called at a given moment, as reported in a backtrace. Each thread in a backtrace will have its own `callstack`. Usually a `callstack` from a [debugger](#debugger) is read from bottom to top: the function at the bottom was called first (the parent) and the function at the top was called last (final child).

See also [calltree](#calltree)

#### calltree

When sampling a program over time multiple pathways through function calls will be observed. While a [calltree](#calltree) captures what functions are being called at a given moment, a `calltree` is one way to represent a summary of the branches of code that executed over a given time interval. This is a common display format used by [tracers](#tracer).

#### backtrace

A backtrace, also known as a stack trace, "is a report of the active stack frames at a certain point in time during the execution of a program". (from https://en.wikipedia.org/wiki/Stack_trace).

The C/C++ language has a method called [backtrace and backtrace_symbols](http://man7.org/linux/man-pages/man3/backtrace_symbols.3.html) that can be used to print out the active stack frames. However, because C/C++ crashes might result from invalid use of memory, or out of memory conditions, it is dangerous to try to run more code, even these functions, after a [crash](#crash). Therefore the convention to get backtraces for crashes is to enable [core file](#core-file) generation and use a [debugger](#debugger) to generate a backtrace after the [crash](#crash) has happened.

Have a look at https://github.com/mapbox/logbt for a project that makes generating backtraces easier, on both Linux and OS X.

#### core_pattern

The core pattern describes where the system should create a [core file](#core-file). Therefore it usually consists of a path on the filesystem and one or more tokens that are populated to dynamically construct the filename.

On Linux, the default core_pattern can be read by doing:

```bash
cat /proc/sys/kernel/core_pattern
```

More info at http://man7.org/linux/man-pages/man5/core.5.html

And on OS X, the default can be read by doing:

```bash
sysctl -n kern.corefile
```

More info at https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man5/core.5.html


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
#### debug symbols

Extra stuff the compiler encodes in a binary to help [debuggers](#debuggers) inspect details about a running or crashed program and [tracers](#tracers) collect detailed information about program execution. Normally to enable debug symbols you pass `-g` to a compiler. This can be done in either a [release build](#release-build) or a [debug build](#debug-build).

#### libc
#### glibc
#### libstdc++
The [GNU implementation of the C++ STL](https://gcc.gnu.org/onlinedocs/libstdc++/).

Note that on OS X, `libstdc++` does not support C++11 or C++14, and so we use `libc++` instead.  ([Read more](https://github.com/mapbox/cpp#standard-c-library).)

#### libc++

The [LLVM implementation of the C++ STL](https://libcxx.llvm.org/)

#### undefined behavior

See http://blog.regehr.org/archives/213 and http://blog.llvm.org/2011/05/what-every-c-programmer-should-know.html

#### versioned symbols
#### abi compatibility


# Memory concepts

#### allocator
#### stack allocation
#### heap allocation
#### custom allocator
#### shared memory
#### memory-mapped file
#### memory leak
#### memory growth
#### memory fragmentation

# Concurrency concepts

#### multi-process
#### multi-threaded
#### single-threaded
#### reentrant
#### thread safety
#### immutability
#### thread safe by locking
#### thread safe by design

# Writing C++

#### template

#### macro

https://gcc.gnu.org/onlinedocs/cpp/Macros.html

#### mutex
#### const

# Node.js & C++

#### Node

A command line tool that consists of a set of bindings to V8 Javascript. These bindings allow you to use Javascript for implementing "lower-level" operations like working with the file system, threads, and scripting servers. More specifically, Node allows you to interact with the [POSIX api](https://github.com/mapbox/cpp/blob/master/glossary.md#posix) (and POSIX-like api for Windows), using Javascript.

Node is made up of a combination of parts:
- V8: to interpret Javscript
- C++ Node bindings: Expose the low-level interface to non-blocking POSIX calls
- Threadpool: To do [file I/O](https://github.com/mapbox/cpp/blob/master/glossary.md#io) or any other blocking system call
- Event Loop: A main process (or main thread) that responds to an event queue: grabs the top item in the event queue, executes it, and then grabs the next item.
- Threadpool and Event Loop Manager ([libuv](http://libuv.org/)): This library helps manage the threadpool and event loop, and helps handle asynchronous [I/O operations](https://github.com/mapbox/cpp/blob/master/glossary.md#io).

See these articles to understand more about node performance:

 - https://www.dynatrace.com/blog/how-to-track-down-cpu-issues-in-node-js/
 - https://www.dynatrace.com/blog/understanding-garbage-collection-and-hunting-memory-leaks-in-node-js/
 
#### V8

V8 is a Javascript "engine", or a Javascript interpreter. It translates Javascript into more efficient machine code (native assembly code), then executes it. V8 gives developers access to functionality (networking, DOM handling, external events, HTML5 video, canvas and data storage) needed to control the web browser, and access to server-side/system functionality within Node.js. V8 is [open source and written in C++](https://github.com/v8/v8).

#### event loop
#### libuv

A library that handles threadpool, event loop, and uses the threading implementation native to the given operating system (for example: Unix uses `pthread`). It is open source, written in C, and is a standalone library most useful as a multithreading interface. Before libuv was available, developers had to manually manage and write threads based on what was provided by the operating system.

Libuv stands for "lib-ultra-event".

#### I/O

An I/O operation that "calls out" from the process to the underlying system. For example, accessing the file system, reading/writing to memory, or sending something over the network

I/O stands for "input/output".

#### threadpool
#### worker
#### C++ bindings
#### non-blocking/blocking
#### [NAN](https://github.com/mapbox/cpp/blob/master/node-cpp.md#nodejs-c-addons)

# FAQ

#### Can you mix binaries between compilers, like g++ and clang++
#### Can you mix binaries between standards, like c++11 and c++14
#### How is C/C++ used in operating systems like Linux and OS X
