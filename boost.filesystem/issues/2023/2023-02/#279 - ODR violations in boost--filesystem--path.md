# #279 - ODR violations in boost::filesystem::path [Closed]

> Username: frankmiller  
> Created at: 2023-02-02 22:17:37 UTC  
> Updated at: 2023-02-08 10:27:50 UTC  
> Closed at: 2023-02-08 10:27:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/279  

There are a number of inline member functions in `boost::filesystem::path` that have different implementations depending on the definition of `BOOST_FILESYSTEM_VERSION`. When compiling boost.filesystem, `BOOST_FILESYSTEM_VERSION` is [always set to 4](https://github.com/boostorg/filesystem/blob/9a49c4f3f4624d445dad875ee3d72f2d364c5e39/include/boost/filesystem/config.hpp#L29). If a program uses boost.filesystem with `BOOST_FILESYSTEM_VERSION` set to 3, [which is the default](https://github.com/boostorg/filesystem/blob/9a49c4f3f4624d445dad875ee3d72f2d364c5e39/include/boost/filesystem/config.hpp#L31), then I believe this is an [ODR violation](https://en.cppreference.com/w/cpp/language/definition) because the program now has multiple definitions of the same function that do not consist of the same sequence of tokens.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-02-03 00:17:11 UTC  
> Url: https://github.com/boostorg/filesystem/issues/279#issuecomment-1414540986  

Duplicates https://github.com/boostorg/filesystem/issues/243.  
  
Yes, this is formally an ODR violation. In practice, though, it works due to forced inlining of these methods. This will get resolved when v3 is dropped.

---

## Comment 2

> Username: frankmiller  
> Created at: 2023-02-03 01:31:17 UTC  
> Url: https://github.com/boostorg/filesystem/issues/279#issuecomment-1414590185  

For the record, there is no inlining in a debug build. Also, I suspect LTO is interacting with inlining in unexpected ways, at least with msvc. I'm happy to resolve the issues I'm seeing on my end by moving to version 4. I just wanted to make sure the upstream maintainers were aware.

---

## Comment 3

> Username: Lastique  
> Created at: 2023-02-03 10:45:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/279#issuecomment-1415680167  

Yes, debug build needs fixing.
