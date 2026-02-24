# #331 Consume utf8_codecvt_facet.hpp instead of utf8_codecvt_facet.ipp [Closed]

> Username: uilianries  
> Created at: 2025-03-22 14:49:21 UTC  
> Updated at: 2025-03-22 18:58:16 UTC  
> Closed at: 2025-03-22 16:24:36 UTC  
> Url: https://github.com/boostorg/filesystem/pull/331  

Hello! When trying to build Boost Filesystem individually, and having all options enabled, I had the follow error:  
  
```  
FAILED: CMakeFiles/boost_filesystem.dir/src/utf8_codecvt_facet.cpp.o   
/usr/bin/c++ -DBOOST_ATOMIC_NO_LIB -DBOOST_ATOMIC_STATIC_LINK -DBOOST_FILESYSTEM_HAS_INIT_PRIORITY -DBOOST_FILESYSTEM_NO_CXX20_ATOMIC_REF -DBOOST_FILESYSTEM_NO_LIB -DBOOST_FILESYSTEM_SOURCE -DBOOST_FILESYSTEM_STATIC_LINK=1 -I/tmp/boost-filesystem/include -I/tmp/boost-filesystem/src -isystem /tmp/boost-depsboost5a5da55e4741d/p/include -isystem /tmp/boost-depsboostfa83194052519/p/include -isystem /tmp/boost-depsboost3e6afbbbad1b2/p/include -isystem /tmp/boost-depsboost534780f2f42ce/p/include -isystem /tmp/boost-depsboost05008c324648d/p/include -isystem /tmp/boost-depsboostfdd92438e9456/p/include -isystem /tmp/boost-depsboost55bb7c3088225/p/include -isystem /tmp/boost-depsboostd5c4bdf511fcf/p/include -isystem /tmp/boost-depsboostc18f335082e58/p/include -isystem /tmp/boost-depsboost0d2259a102ec0/p/include -isystem /tmp/boost-depsboosta27ed4cbd95d9/p/include -isystem /tmp/boost-depsboost709aa7629d754/p/include -isystem /tmp/boost-depsboost9620801486dfa/p/include -isystem /tmp/boost-depsboost6e0cea910a35e/p/include -isystem /tmp/boost-depsboost57c5bd6936a77/p/include -isystem /tmp/boost-depsboost8855dda7c06e3/p/include -isystem /tmp/boost-depsboost96b0ff550425d/p/include -isystem /tmp/boost-depsboostce6112f35f123/p/include -isystem /tmp/boost-depsboostcacd125550147/p/include -isystem /tmp/boost-depsboost6095d7bbf4d8d/p/include -isystem /tmp/boost-depsboostb808b762eeddb/p/include -isystem /tmp/boost-depsboost4d493fb251f4c/p/include -isystem /tmp/boost-depsboost319af955bd393/p/include -isystem /tmp/boost-depsboost6a18d9819c200/p/include -isystem /tmp/boost-depsboost201697502b128/p/include -isystem /tmp/boost-depsboostd7ef083bd9a4a/p/include -isystem /tmp/boost-depsboost6506dee3ab4c2/p/include -isystem /tmp/boost-depsboost88bc4394d250c/p/include -isystem /tmp/boost-depsboost05e6b6315e3db/p/include -isystem /tmp/boost-depsboost424940f48f3b7/p/include -isystem /tmp/boost-depsboost6f1722fb8171c/p/include -m64 -O3 -DNDEBUG -std=gnu++17 -fPIC -MD -MT CMakeFiles/boost_filesystem.dir/src/utf8_codecvt_facet.cpp.o -MF CMakeFiles/boost_filesystem.dir/src/utf8_codecvt_facet.cpp.o.d -o CMakeFiles/boost_filesystem.dir/src/utf8_codecvt_facet.cpp.o -c /tmp/boost-filesystem/src/utf8_codecvt_facet.cpp  
/tmp/boost-filesystem/src/utf8_codecvt_facet.cpp:23:10: fatal error: boost/detail/utf8_codecvt_facet.ipp: No such file or directory  
   23 | #include <boost/detail/utf8_codecvt_facet.ipp>  
      |          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
compilation terminated.  
  
```  
  
The error comes from https://github.com/boostorg/filesystem/blob/boost-1.87.0/src/utf8_codecvt_facet.cpp#L23  
  
The file `utf8_codecvt_facet.ipp` does not exist, instead, it should be https://github.com/boostorg/filesystem/blob/boost-1.87.0/include/boost/filesystem/detail/utf8_codecvt_facet.hpp  
  
Regards!

---

## Comment 1

> Username: Lastique  
> Created_at: 2025-03-22 16:24:37 UTC  
> Url: https://github.com/boostorg/filesystem/pull/331#issuecomment-2745343067  

This change is incorrect. `utf8_codecvt_facet.ipp` [does exist](https://github.com/boostorg/detail/blob/boost-1.87.0/include/boost/detail/utf8_codecvt_facet.ipp) and contains implementation of the facet that is supposed to be compiled as an internal part of Boost.Filesystem.  
  
Please, check that your Boost distribution is intact and that there is no other Boost version in the include paths on your system.

---

## Comment 2

> Username: uilianries  
> Created_at: 2025-03-22 18:58:15 UTC  
> Url: https://github.com/boostorg/filesystem/pull/331#issuecomment-2745410851  

@Lastique Sorry about the noise!

---
