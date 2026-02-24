# #38 - When using boost as submodule, further `FindBoost` fail. [Closed]

> Username: greg7mdp  
> Created at: 2023-05-04 14:30:01 UTC  
> Updated at: 2023-05-07 13:52:19 UTC  
> Closed at: 2023-05-07 13:52:19 UTC  
> Url: https://github.com/boostorg/cmake/issues/38  

I'd like to use boost as a submodule. As such I have added the following in my project:  
  
```  
set( BOOST_INCLUDE_LIBRARIES iostreams date_time system program_options chrono test )  
add_subdirectory( boost EXCLUDE_FROM_ALL )  
```  
  
However, there are further `add_subdirectory` in my project, which add other external submodules. These submodules call `find_package` which fails:  
  
```  
find_package(Boost REQUIRED COMPONENTS date_time chrono iostreams)  
```  
  
and the error:  
  
```  
> cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_EXPORT_COMPILE_COMMANDS=1 -DCMAKE_PREFIX_PATH=/usr/lib/llvm-11 -DCMAKE_CXX_COMPILER=clang++-16 -DCMAKE_C_COMPILER=clang-16 -DCMAKE_CXX_FLAGS="-g -DBOOST_ALLOW_DEPRECATED_HEADERS -DBOOST_BIND_GLOBAL_PLACEHOLDERS" -GNinja ..  
-- Configuring Leap on Linux  
-- Using 'SYS' as CORE symbol name  
-- Using 'EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV' as public key for 'eosio' account  
-- Boost: Release build, static libraries, MPI OFF, Python OFF, testing OFF  
-- Boost: libraries included: iostreams;date_time;system;program_options;chrono;test  
-- Boost.Iostreams: ZLIB ON, BZip2 OFF, LZMA ON (multithreaded), Zstd OFF  
-- Found the following ICU libraries:  
--   data (required): /usr/lib/x86_64-linux-gnu/libicudata.so  
--   i18n (required): /usr/lib/x86_64-linux-gnu/libicui18n.so  
--   uc (required): /usr/lib/x86_64-linux-gnu/libicuuc.so  
CMake Error at /opt/cmake/cmake-3.24.3-linux-x86_64/share/cmake-3.24/Modules/FindPackageHandleStandardArgs.cmake:230 (message):  
  Could NOT find Boost (missing: Boost_INCLUDE_DIR date_time chrono  
  iostreams)  
Call Stack (most recent call first):  
  /opt/cmake/cmake-3.24.3-linux-x86_64/share/cmake-3.24/Modules/FindPackageHandleStandardArgs.cmake:594 (_FPHSA_FAILURE_MESSAGE)  
  /opt/cmake/cmake-3.24.3-linux-x86_64/share/cmake-3.24/Modules/FindBoost.cmake:2376 (find_package_handle_standard_args)  
  libraries/libfc/CMakeLists.txt:94 (find_package)  
  
  
-- Configuring incomplete, errors occurred!  
```  
  
I guess that the further `find_package(Boost ...)` fails because boost hasn't been built yet. Is there a way around this?

---

## Comment 1

> Username: pdimov  
> Created at: 2023-05-04 17:05:13 UTC  
> Url: https://github.com/boostorg/cmake/issues/38#issuecomment-1535103357  

`add_subdirectory` and `find_package` aren't intended to be used together. `add_subdirectory` makes the library a subproject, whereas `find_package` finds a prebuilt and preinstalled library. There are various hacks that can intercept `find_package` and make it succeed in such a scenario (some of them described [here](https://cmake.org/cmake/help/latest/guide/using-dependencies/index.html)), but none of them are particularly reliable in the general case, because the library issuing the `find_package` call may rely on arbitrary CMake variables set by the Find script.  
  
In the specific case of `find_package(Boost)`, some libraries rely on the `Boost_INCLUDE_DIRECTORIES` and `Boost_LIBRARIES` variables, while others link to the `Boost::...` targets. I don't exactly know what you're trying to build, but if it's some variation of [this](https://github.com/EOSIO/fc/blob/863dc8d371fd4da25f89cb08b13737f009a9cec7/CMakeLists.txt), it seems to [only link to the Boost targets](https://github.com/EOSIO/fc/blob/863dc8d371fd4da25f89cb08b13737f009a9cec7/CMakeLists.txt#L194), so you might be able to get it to work by just commenting out the `find_package` call (because the targets have already been created by `add_subdirectory`.)

---

## Comment 2

> Username: greg7mdp  
> Created at: 2023-05-04 17:20:34 UTC  
> Updated at: 2023-05-07 13:52:11 UTC  
> Url: https://github.com/boostorg/cmake/issues/38#issuecomment-1535137825  

Thanks a lot for the answer, @pdimov !   
The solution for this issue is to avoid doing the `find_package()` in other submodules (with a `if` such as `if(NOT boost_headers_SOURCE_DIR)`), and the targets provided by the `add_subdirectory(boost)`) can be used without problems everywhere.
