# #62 - Shared and Static variant cannot coexist in installation prefix on Windows [Closed]

> Username: sodevel  
> Created at: 2024-05-27 20:00:51 UTC  
> Updated at: 2024-06-07 16:00:09 UTC  
> Closed at: 2024-06-07 16:00:09 UTC  
> Url: https://github.com/boostorg/cmake/issues/62  

I used to build Boost with `b2` but since this is currently not working with Visual Studio 2022 17.10 i discovered there is a CMake build available since some time. I used to build 4 variants on Windows, Debug and Release as Static and DLL, and installed them in the same target location, this was working without problems. However, this doesn't work when using CMake.  
  
I am building Boost like this:  
  
```cmd  
cmake -S . -B "build-cmake-%LIBRARY_TARGET%-dll" -G "Ninja Multi-Config" -DCMAKE_INSTALL_PREFIX="%LIBRARY_INSTALL_PREFIX%" -DBOOST_INSTALL_LAYOUT=versioned -DBUILD_SHARED_LIBS=ON -DBOOST_RUNTIME_LINK=shared  
  
...  
  
cmake -S . -B "build-cmake-%LIBRARY_TARGET%-static" -G "Ninja Multi-Config" -DCMAKE_INSTALL_PREFIX="%LIBRARY_INSTALL_PREFIX%" -DBOOST_INSTALL_LAYOUT=versioned -DBUILD_SHARED_LIBS=OFF -DBOOST_RUNTIME_LINK=shared  
```  
  
While the libraries themself still do coexist, the CMake target configurations get overwritten. In the example above, only the static targets are present, the DLL targets are gone.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-05-27 23:22:10 UTC  
> Url: https://github.com/boostorg/cmake/issues/62#issuecomment-2134134762  

CMake doesn't have a good answer for the question of installing both shared and static variants of a library.  
  
I suppose the best you could do today is install the CMake config files into two separate directories, one for static, one for shared, by setting BOOST_INSTALL_CMAKEDIR.  
  
Since they define the same targets, they can't go into the same directory anyway.

---

## Comment 2

> Username: sodevel  
> Created at: 2024-05-28 14:59:40 UTC  
> Url: https://github.com/boostorg/cmake/issues/62#issuecomment-2135448967  

Thanks for the quick response. I investigated a bit more, the CMake build of Boost also does not honor `Boost_USE_STATIC_LIBS` like the `b2` build, that build uses a different naming scheme for the target files which allows coexistence and that variable is used to select the desired variant. Is there a reason why the CMake build uses a different structure for the metadata files?  
  
Using custom directories for the CMake files require a modifcation of the `find_package()` calls, together with the not respected CMake variable apparently it is not possible to use the CMake build as drop-in replacement for the `b2` build in my use-case.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-05-28 15:05:07 UTC  
> Url: https://github.com/boostorg/cmake/issues/62#issuecomment-2135462033  

The CMake build basically tries to match CMake's default behavior when possible; `Boost_USE_STATIC_LIBS` is a variable specific to FindBoost that is not CMake convention.  
  
But it might be better if we honor it.  
  
I suppose I can change this line  
  
https://github.com/boostorg/cmake/blob/4c6884c2e25e316029f016851840cea10f2b334b/include/BoostInstall.cmake#L285  
  
to install the config files for static/shared libraries in e.g. `boost_atomic-1.86.0-static` and `boost_atomic-1.86.0-shared`, respectively, instead of `boost_atomic-1.86.0`, and then change the generated config files to look at `Boost_USE_STATIC_LIBS` and, if that's explicitly set, to drop the non-matching library from consideration.

---

## Comment 4

> Username: sodevel  
> Created at: 2024-05-28 15:47:48 UTC  
> Url: https://github.com/boostorg/cmake/issues/62#issuecomment-2135583227  

That sounds good, the `b2` build does something like that too. That would solve my use-case because i can't get the `BOOST_INSTALL_CMAKEDIR` variant to work (easily), i haven't found a way to inject a path segment into the middle of the search directories, only at end, but that doesn't help and i don't want to use absolute paths.

---

## Comment 5

> Username: pdimov  
> Created at: 2024-05-28 15:51:02 UTC  
> Url: https://github.com/boostorg/cmake/issues/62#issuecomment-2135589723  

Yeah, I looked at the `find_package` search paths and there's no good way to put the shared/static configs in separate BOOST_INSTALL_CMAKEDIRs and have them be found automatically (without setting the path explicitly in the CMakeLists.txt file.)  
  
Whereas appending -shared or -static to the subdirectory works out of the box and seems to be backward-compatible in that existing uses will still work.

---

## Comment 6

> Username: pdimov  
> Created at: 2024-05-29 02:39:11 UTC  
> Url: https://github.com/boostorg/cmake/issues/62#issuecomment-2136412800  

I've implemented this on the develop branch.

---

## Comment 7

> Username: sodevel  
> Created at: 2024-05-30 19:05:31 UTC  
> Updated at: 2024-05-30 19:24:10 UTC  
> Url: https://github.com/boostorg/cmake/issues/62#issuecomment-2140701700  

Thank you very much for addressing this so quickly. I just tested your implementation, sadly it's not fully working. I found the cause but cannot fix it myself, i'm hitting following error if using the shared variant:  
  
```  
[cmake] CMake Error at C:/Program Files/CMake/share/cmake-3.29/Modules/CMakeFindDependencyMacro.cmake:76 (find_package):  
[cmake]   Could not find a configuration file for package "boost_exception" that  
[cmake]   exactly matches requested version "1.86.0".  
[cmake]   
[cmake]   The following configuration files were considered but not accepted:  
[cmake]   
[cmake]     C:/devel/cmake/msvc/14.3/x86/boost/boost-1.71.0.beta1-3435-gbe6765ef52-dirty/lib/cmake/boost_exception-1.86.0-static/boost_exception-config.cmake, version: 1.86.0 (static)  
[cmake]   
[cmake] Call Stack (most recent call first):  
[cmake]   C:/devel/cmake/msvc/14.3/x86/boost/boost-1.71.0.beta1-3435-gbe6765ef52-dirty/lib/cmake/boost_coroutine-1.86.0-shared/boost_coroutine-config.cmake:22 (find_dependency)  
[cmake]   C:/devel/cmake/msvc/14.3/x86/boost/boost-1.71.0.beta1-3435-gbe6765ef52-dirty/lib/cmake/Boost-1.86.0/BoostConfig.cmake:67 (find_package)  
[cmake]   C:/devel/cmake/msvc/14.3/x86/boost/boost-1.71.0.beta1-3435-gbe6765ef52-dirty/lib/cmake/Boost-1.86.0/BoostConfig.cmake:128 (boostcfg_find_component)  
[cmake]   C:/Program Files/CMake/share/cmake-3.29/Modules/FindBoost.cmake:594 (find_package)  
[cmake]   CMakeLists.txt:37 (find_package)  
[cmake]   
```  
  
In my build the library `boost_exception` is together with `boost_test_exec_monitor` the only library that exists only in a static version. I checked a `b2` build of boost, it looks like `boost_exception` is empty, i haven't found a location where this library does get added to the target. For `boost_test_exec_monitor` in both cases the static library gets added to the target.  
  
It looks like these two libraries need a special handling.

---

## Comment 8

> Username: pdimov  
> Created at: 2024-05-30 19:24:20 UTC  
> Url: https://github.com/boostorg/cmake/issues/62#issuecomment-2140728188  

Yes, `boost_exception` is hardcoded to static. I'll look into that.

---

## Comment 9

> Username: pdimov  
> Created at: 2024-05-31 03:12:05 UTC  
> Url: https://github.com/boostorg/cmake/issues/62#issuecomment-2141158417  

Should be fixed now.

---

## Comment 10

> Username: sodevel  
> Created at: 2024-05-31 20:01:40 UTC  
> Url: https://github.com/boostorg/cmake/issues/62#issuecomment-2142904091  

Thanks, it is working flawless for me now.
