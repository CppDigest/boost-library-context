# #64 - Howto find header-only libraries properly [Open]

> Username: sodevel  
> Created at: 2024-06-07 15:58:05 UTC  
> Updated at: 2024-06-07 16:43:27 UTC  
> Url: https://github.com/boostorg/cmake/issues/64  

I am not sure if this is the right place to ask this question, but since it's CMake related, i give it a try.  
  
First some background, i am using CMake to build my projects, most of my projects link against an installed Boost library, only very few projects compile a private version of Boost themself during their build and link against this one. Until recently i was using only B2 builds of Boost, but now i switched to CMake builds, mostly to profit of the transitive dependency resolution to reduce compilation for projects that build Boost themself. Of course i was hit by the problem that the target `Boost::headers` does nothing in that case and i have to link against header-only libraries directly.  
  
Now i'm facing the difficulty that a big library of mine has to work in both environments, Boost as subproject and an external Boost. To find the external Boost header-only libraries i use the usual `find_package(Boost 1.84.0 REQUIRED headers)` call, this gives me `Boost::headers` to link against. This target also exists when using Boost as subproject, but in that case it is not `IMPORTED`. My first idea was, check if that target is `IMPORTED`, use it in that case, otherwise link against indiviual libraries. Doesn't look nice, but would work.  
  
Looking at the CMake metadata of a CMake Boost build i saw that there is code to actually create targets for individual header-only libraries, but for this to get actually used, i also have to search for these libraries explicit like `find_package(Boost 1.84.0 REQUIRED asio smart_ptr)`. I like that approach but there is one problem, this does not work for a B2 build, that one does not contain targets for individual header-only libraries.  
  
Now i wonder, what would be a future proof way to search for header-only libraries?

---

## Comment 1

> Username: pdimov  
> Created at: 2024-06-07 16:01:56 UTC  
> Url: https://github.com/boostorg/cmake/issues/64#issuecomment-2155123345  

Support for a "real" Boost::headers target has been added and will be available in the next release; see https://github.com/boostorg/cmake/issues/61.  
  
It's not on by default, though; you have to set BOOST_ENABLE_COMPATIBILITY_TARGETS=ON.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-06-07 16:02:59 UTC  
> Url: https://github.com/boostorg/cmake/issues/64#issuecomment-2155125074  

See https://github.com/boostorg/cmake/commit/0994f04d69a235ae51a387f67cf25064a217a343 and https://github.com/boostorg/cmake/commit/33c036bd70aa77444cc474837ddd93551d5d1aed.

---

## Comment 3

> Username: sodevel  
> Created at: 2024-06-07 16:43:25 UTC  
> Url: https://github.com/boostorg/cmake/issues/64#issuecomment-2155184407  

Ah thanks, so everything stays as-is.
