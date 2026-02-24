# #1 Add CMake support [Merged]

> Username: gracicot  
> Created at: 2020-08-06 12:19:21 UTC  
> Updated at: 2020-08-07 09:11:53 UTC  
> Merged at: 2020-08-07 09:11:52 UTC  
> Closed at: 2020-08-07 09:11:53 UTC  
> Url: https://github.com/boostorg/redis/pull/1  

This patch enable CMake support for building the library and installing it. By default, it will build the `examples` and `tests` target just like the current makefile. It also add a `test` target that run tests, and also an `install` target so the library is installed into the system (or any specified directory).  
  
To make building the library easier, I added a `conanfile.txt` so dependencies can be installed automatically. I made a setup that is doing so without affecting how the targets are exported. It can be disabled by `-DAEDIS_USE_CONAN=OFF`. To use it, simply create the build directory (for example, `build/`), cd into it and type `conan install ..` then run CMake `cmake ..`  
  
It also supports using the library from the build tree directly. So a CMake project can add `path/to/aedis/build` to the `CMAKE_PREFIX_PATH` variable and `find_package(aedis)` will simply work without even installing the library.   
  
Here's a couple things to consider:  
  
 - No version file is exported, so users won't be able to use `find_package(aedis 0.1.1)` syntax to check for version compatibility, since this library is not versioned.  
 - The old makefile is still there. It could be removed.  
 - The header is not in a `include/aedis` directory, making users `#include <aedis.h>` instead of `#include <aedis/aedis.h>`  
   - This can be done without changing the project structure.  
 - Conan is enabled by default in the CMake file.

---

## Comment 1

> Username: mzimbres  
> Created_at: 2020-08-06 12:55:24 UTC  
> Url: https://github.com/boostorg/redis/pull/1#issuecomment-669909245  

Will have a look soon, thanks.

---

## Comment 2

> Username: mzimbres  
> Created_at: 2020-08-07 09:11:38 UTC  
> Url: https://github.com/boostorg/redis/pull/1#issuecomment-670417995  

I agree with your points about the versions file, the directory `include/aedis` etc. Those are planned features, you can open issues for that if you like.

---
