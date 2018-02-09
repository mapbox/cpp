# Debugging crashes

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

# Errors

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
  - Overriding the `-mmacosx-version-min` flag.

This warning is harmless until Apple decides to remove `libstdc++` from an OS X version. For modules that don't explicitly link to libc++ (like recommended in the solution above) this warning will persist until either:

 - the nodejs project starts linking to libc++ explicitly (refs https://github.com/nodejs/node/commit/7292a1e954e0db348ff78c704df9e105ba5667ad)
 - or the nodejs project changes [this line](https://github.com/nodejs/node/blob/88323e874473d18cce22d6ae134a056919c457e4/common.gypi#L355) to `10.8`
