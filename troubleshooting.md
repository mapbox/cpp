## Troubleshooting

A listing of errors and solutions.


### This version of node/NAN/v8 requires a C++11 compiler

Error:

```
../node_modules/nan/nan.h:45:3: error: #error This version of node/NAN/v8 requires a C++11 compiler
```

#### Solution

Upgrade your compiler using this method: https://github.com/mapbox/cpp#standard-compiler

#### Context:

You are building a node c++ module. That error above was happening because node itself (node v4 and greater) needs a compiler that supports C++11. You are likely building on a linux system with an old default compiler like g++ 4.8 or older.

### clang: warning: libstdc++ is deprecated

On OSX you hit an error like:

```
clang: warning: libstdc++ is deprecated; move to libc++ with a minimum deployment target of OS X 10.9
```

#### Solution:

This is just a warning - it is harmless. However to fix the warning:

 - If you are the developer of the module: add [these lines](https://github.com/mapbox/node-cpp-skel/blob/bf5fa91961c4027e709b557f5d3acd7eef8a7894/binding.gyp#L20-L21) to your `binding.gyp`
 - If you are the user of the module, file a bug report upstream.

#### Context

You are compiling a node c++ module on OS X. This warning is expected if you are running OS X >= 10.12 and:

  - You are building a node c++ addon against the nodejs provided binary
  - That c++ addon is not explicitly requesting linking to libc++ or;
  - Overriding the ``-mmacosx-version-min` flag.

This warning is harmless until Apple decides to remove `libstdc++` from an OS X version. For modules that don't explicitly link to libc++ (like recommended in the solution above) this warning will persist until either:

 - the nodejs project starts linking to libc++ explicitly (refs https://github.com/nodejs/node/commit/7292a1e954e0db348ff78c704df9e105ba5667ad)
 - or the nodejs project changes [this line](https://github.com/nodejs/node/blob/88323e874473d18cce22d6ae134a056919c457e4/common.gypi#L355) to `10.8`