Make tips
=========

author: @ericfischer

The basic idea behind Makefiles is pretty simple: each rule says what file it makes (the "target"), what other files have to exist before it can (the "prerequisites"), and what shell commands it has to run to in order to make it happen. When you run `make`, you say what file you want, and it figures out the sequence of rules that have to happen to get that result.

Rules
-----

A Makefile that builds a program typically has a specific rule that links that program together from a series of object files and libraries, like this:

```
program: main.o util.o other.o
        c++ -o $@ $^ -lm -lsqlite3
```

and a generic rule that compiles an object file from a source file:

```
%.o: %.cpp
        c++ -c -o $@ -O3 $<
```

In this case, since `program` wants `main.o`, `util.o`, and `other.o`, and there is a general rule to make `.o` from `.cpp` files, the two rules together will produce `program` from `main.cpp`, `util.cpp`, and `other.cpp`.

You tell `make` to run the rule that produces `program` with the shell command

```
make program
```

If you run `make` without specifying what rule to run, it makes whatever rule target is the first one in the Makefile. In a Makefile that can produce many targets, it is conventional for the first target to be named `all`, and to have its prequisites be all the programs are part of the project. So this Makefile should probably begin with

```
all: program
```

to make it explicit that making `program` is what this Makefile does.

Note that there is no shell command associated with this rule. It is entirely a target (`all`) and a prerequisite (`program`), and doesn't do anything on its own after ensuring that the prequisites exist. It does not actually create a file called `all`, so if you run `make` again, it will perform the rule again.

Magic special characters
------------------------

There are already a lot of mysterious special characters in this tiny Makefile. You can read the list of many more of them in the [GNU Make Automatic Variables](https://www.gnu.org/software/make/manual/html_node/Automatic-Variables.html) page, but the important ones here are:

* `$@` is the name of the file that is being made. So in the first rule, `$@` is `program`, and in the second, `$@` is the name of the `.o` file that is being made from whatever `.cpp` file is being compiled.
* `$^` is the list of all the prerequisites for the file that is being made. So in the first rule, `$^` is `main.o util.o other.o`, since these are the three object files that go into that program.
* `$<` is the *first* prequisite for the file that is being made. So in the second, rule, `$<` is the name of the single `.cpp` source file that is being compiled.
* `%` is a wildcard in generic rules. If you had a rule that made PDF files from PostScript files, you would specify it as `%.pdf: %.ps` in the same way that this rule generically makes `.o` files from `.cpp` files.

The reason the generic rule uses `$<` instead of `$^` is that the object file might be specified elsewhere in the Makefile to have other, indirect prerequisites, for instance some header files that the `.cpp` file #includes. These are specified as prerequisites so that the `.o` file gets rebuilt when they change, but they are included indirectly and shouldn't be given as direct command line arguments to `c++`. So the rule uses `$<` to refer to the one specific source file that is being compiled.

There is also an almost invisible special character: the `        ` that precedes each shell command in a rule is a *tab* character, not a series of spaces. If you use spaces instead of a tab, your Makefile will not work.

Variables
---------

It is often useful to use variables in your Makefile for configuration options. For example, the rules above explicitly refer to the C++ compiler as `c++`, which leaves you without an easy way to specify that you want to use a specific version of a specific compiler instead of the operating system default. It also doesn't give you a way to specify compiler options for optimization or libraries or debugging.


See also
--------

https://bost.ocks.org/mike/make