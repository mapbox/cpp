# Code coverage

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
