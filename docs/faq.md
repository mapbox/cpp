## Should I install `libstdc++6` instead of `libstdc++-5-dev`?

The `tl;dr` is yes you can - there is no harm - but don't please. Please install `libstdc++-5-dev` instead - it is more explicit.

The longer version is that installing the `libstdc++6` package is functionally the same as installing `libstdc++-5-dev`. The difference is that `libstdc++-5-dev`:

  - Brings in a few more headers (nothing so big to worry about)
  - If you need the ability to compile c++14 programs you need these headers
  - The `libstdc++-5-dev` package is wonderfully named!

Wonderfully named you say? Yes, it is named logically and that is a wonderful thing. The name clearly indicates that `libstdc++` is being installed. The version `5` indicates that you are getting the version compatible with g++-5 and its respective headers. And it is the development version so the version you use if you need libstdc++ headers (the files put into {prefix}/include and the right libstdc++ library to be installed (the dynamic (aka shared) library put into `{prefix}/lib`).

The `libstdc++6` package name can be confusing. Note that there is no dash between the `++` and the `6` like there is between the `++` and the `5` in the dev package.

So why the `6`? It is the soname 6. Sonames are a linux way of speaking about binary library compatibility (ABI). The soname is not the version! The soname also does not really capture all you need to know about binary capability because libstdc++ also uses [versioned symbols](https://github.com/springmeyer/glibcxx-symbol-versioning). Because of versioned symbols you can live a free and exciting life always running the latest libc or libstdc++ without fear because it is forward compatible (binaries only use the versioned symbols they were compiled against).

**So what version do you actually get if you install `libstdc++6` or `libstdc++-5-dev`?**

It depends :) The important thing to remember is that unless you know better you want the latest version. If you really need to know then you can do:

```
apt-cache show libstdc++6
```

You will get the latest version packaged in https://launchpad.net/~ubuntu-toolchain-r/+archive/ubuntu/test/+packages for ubuntu precise and trusty. At the time of this writing the version is `v6.1.1`. Again, that `6` is not related to the soname.

Btw, if you ran `apt-get show libstdc++6` on ubuntu precise without the `ubuntu-toolchain-r` PPA installed then `apt-get install libstdc++6` would give libstdc++ `v4.6.3`.

If you hit a runtime error like `/usr/lib/x86_64-linux-gnu/libstdc++.so.6: version GLIBCXX_3.4.20' not found` it means you forgot to upgrade libstdc++6 to at least `v6.1.1`.

One caveat: To upgrade libstdc++ for binary programs that don't need to be compiled, just installing `libstdc++6` is enough. You don't need the extra headers brought in by `libstdc++-5-dev`. But there is no harm having the headers installed and being explicit will help make clear in the future when we require the g++-6 version of libstdc++ and that we've upgraded to it by requiring `libstdc++-6-dev`.

For much more detail about which libstdc++ versions are available on which ubuntu versions (and upgradable via which packages) see https://github.com/springmeyer/glibcxx-symbol-versioning

## I'm seeing a compiler error like "no template named 'foo' in namespace 'std'". What is that about?

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
