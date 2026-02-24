# #186 Remove dependency on Boost.random. [Open]

> Username: Romain-Geissler-1A  
> Created at: 2025-07-25 13:03:20 UTC  
> Updated at: 2025-07-25 13:05:30 UTC  
> Url: https://github.com/boostorg/iostreams/pull/186  

Boost.iostreams now requires C++11, so normally a <random> implementation shall exists in the compiling toolchain, so use this instead of the Boost implementation.  
  
This allows to no longer depend explicitly on libboost_random.so, which is now the case with the recent modular works (see other case of these extra dependencies being added with modular changes in https://github.com/boostorg/regex/issues/253 or  
https://github.com/boostorg/system/issues/132)

---
