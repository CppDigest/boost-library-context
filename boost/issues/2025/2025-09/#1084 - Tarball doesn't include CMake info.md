# #1084 - Tarball doesn't include CMake info [Open]

> Username: rickmark  
> Created at: 2025-09-17 03:48:05 UTC  
> Updated at: 2025-09-17 04:03:47 UTC  
> Url: https://github.com/boostorg/boost/issues/1084  

The released tarballs do not include CMake based build data.  
  
This makes it impossible for things like home-brew to install CMake modules therefore CMake based dependency on header only libraries such as libboost_system are broken for macOS.  
  
There's no good reason to filter this, and there needs to be better support from `b2` to install the proper CMake data for depending on non dylib/a/so based header only libraries.  
  
I believe the best option is to have macOS use `cmake` based builds, but this cannot happen without the tarball including the proper data for it.  
  
  
  
## Replication Steps:  
  
### To install brew without CMake  
  
```sh  
brew install boost  
```  
  
Look for `libboost_system` in any cmake project  
  
### To test for cmake  
  
Try and install `gr-osmocom` on macOS

---

## Comment 1

> Username: rickmark  
> Created at: 2025-09-17 04:03:47 UTC  
> Url: https://github.com/boostorg/boost/issues/1084#issuecomment-3301226841  

I do think this is worth a `1.89.1` tarball since this is in fact a breaking problem...
