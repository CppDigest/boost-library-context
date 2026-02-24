# #131 - Remove unnecessary C++11 requirements [Closed]

> Username: Lastique  
> Created at: 2025-06-14 15:28:11 UTC  
> Updated at: 2025-06-16 19:19:46 UTC  
> Closed at: 2025-06-16 19:19:46 UTC  
> Url: https://github.com/boostorg/random/issues/131  

Boost.Random `Jamfile` [lists](https://github.com/boostorg/random/blob/7363de5e94a524822cd26e04767b84690cca94b1/build/Jamfile.v2#L27) a number of C++11 requirements that are actually not needed by the library. In particular, it lists `cxx11_thread_local`, but `thread_local` is not used anywhere in the code base. I'm not sure if there are other requirements that are not actually used, but at least `cxx11_hdr_chrono` seems unnecessary as well.  
  
These extra requirements make Boost.Random, and all downstream libraries that depend on it, incompatible with compilers that could otherwise be compatible. For example, it makes it incompatible with 32-bit gcc 8.1 from MinGW-w64, which doesn't support `cxx11_thread_local`. This affects Boost.Log, which depends on Boost.Random, as it prevents it from building and testing on this compiler. Here's an example of a skipped build:  
  
https://ci.appveyor.com/project/Lastique/log/builds/52232706/job/copm19vcmcbriv50
