## Glossary

#### Terms

##### Building C++

- compiler
- linker
- posix
- dynamic/shared library
- static library
- object file
- executable
- header: .hpp file (also .h)
- header-only library: when all of the source code is in the .hpp file (none in the .cpp)
- precompiled library: when source code is in .cpp files that is compiled and linked into a shared (aka dymamic) library (.so on linux, .dylib on os x)
- when to write compiled library vs a header only library?
 - hide dependencies that are only needed at runtime and not at compile time by users of library
 - when you want to ship binary code that you don't want to recompile or re-link
- c++11: https://isocpp.org/wiki/faq/cpp11-language
- c++14: https://isocpp.org/wiki/faq/cpp14-language
- c++1y
- c++98
- c++11 move semantics (std::move)
- gcc
- clang
- g++
- clang++
- versioned symbols
- libc
- glibc
- libstdc++
- libc++
- undefined behavior
- versioned symbols
- abi compatibility

##### Writing C++

- template
- macro
- mutex
- const

#### Topics

 - Can you mix binaries between compilers, like g++ and clang++
 - Can you mix binaries between standards, like c++11 and c++14
 - How is C/C++ used in operating systems like Linux and OS X