# #110 CMake install and VS2015 support [Closed]

> Username: e-fominov  
> Created at: 2016-10-05 08:43:09 UTC  
> Updated at: 2017-07-25 17:44:16 UTC  
> Closed at: 2017-02-27 17:08:25 UTC  
> Url: https://github.com/boostorg/beast/pull/110  

With this changes CMake will compile VS2015 and MinGW with automatic boost libraries linkage  
  
Also CMake can be called like this:  
  
```  
cmake .. -G"Visual Studio 14 2015" -DCMAKE_INSTALL_PREFIX=c:\lib  
cmake --build . --target install  
```  
  
This will install Beast headers into c:\lib\inlcude\beast and create cmake files needed for Beast include into other projects. Default CMAKE_INSTALL_PREFIX (defined by cmake) value is inside c:\program files folder, and requires Administrator privileges  
  
In some other project now you can include Beast (CmakeFiles.txt)  
  
```  
find_package(Beast REQUIRED)  
include_directories(${Beast_INCLUDE_DIRS})  
```  
  
to build this on Windows platform you will need to pass CMAKE_PREFIX_PATH argument:  
`cmake .. -DCMAKE_PREFIX_PATH=c:\lib`  
  
Or if Beast is installed in Program files - nothing will be needed  
On Linux platform this will not require CMAKE_PREFIX_PATH

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-10-05 08:55:42 UTC  
> Url: https://github.com/boostorg/beast/pull/110#issuecomment-251619347  

[![Coverage Status](https://coveralls.io/builds/8192598/badge)](https://coveralls.io/builds/8192598)  
  
Coverage remained the same at 98.095% when pulling **13ddc64e53835045dda32786ae0f2c9fc2db18fb on e-fominov:cmake_scripts** into **70b8555cdca69b9c5777db02115d30d1c1aad631 on vinniefalco:master**.

---

## Comment 2

> Username: codecov-io  
> Created_at: 2016-10-05 09:00:35 UTC  
> Updated_at: 2016-11-19 00:40:07 UTC  
> Url: https://github.com/boostorg/beast/pull/110#issuecomment-251620425  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/110?src=pr) is 98.05% (diff: 100%)  
  
> Merging [#110](https://codecov.io/gh/vinniefalco/Beast/pull/110?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will increase coverage by **0.48%**  
  
``` diff  
@@             master       #110   diff @@  
==========================================  
  Files            71         71            
  Lines          4193       4054   -139     
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
- Hits           4091       3975   -116     
+ Misses          102         79    -23     
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [7b4de4b...8f1724a](https://codecov.io/gh/vinniefalco/Beast/compare/7b4de4b9513105845181321ba672bea4ce4a9062...8f1724a12a33ec94fd4b4a1c0d90034b5c97229a?src=pr)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2016-10-09 21:25:36 UTC  
> Url: https://github.com/boostorg/beast/pull/110#issuecomment-252513686  

@seelabs @mellery451 What do you guys think about having an "install" target?

---

## Comment 4

> Username: mellery451  
> Created_at: 2016-10-10 15:08:43 UTC  
> Url: https://github.com/boostorg/beast/pull/110#issuecomment-252651360  

install target seems like a good idea and this implementation looks right to me.

---

## Review 5 [Approved]

> Username: mellery451  
> Created_at: 2016-10-10 15:59:50 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/110#pullrequestreview-3526823  

one minor comment - otherwise 👍

📁 CMakeLists.txt

```diff
 115 |+ string(REGEX MATCH "\"\.+\"" Beast_VERSION_STRING ${Beast_VERSION_STRING})
 116 |+ string(REPLACE "\"" "" Beast_VERSION_STRING ${Beast_VERSION_STRING})
 117 |+ set(VERSION ${Beast_VERSION_STRING})
```

> Username: mellery451  
> Created_at: 2016-10-10 15:59:20 UTC  
> Updated_at: 2017-02-01 23:48:13 UTC  
> Url: https://github.com/boostorg/beast/pull/110#discussion_r82632783  

minor: I don't see any particular need for the `VERSION` variable - I believe you could just use `${Beast_VERSION_STRING}` in the `write_basic_package_version_file` invocation instead.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2016-10-10 16:10:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/110#pullrequestreview-3528650  

📁 scripts/beastConfig.cmake.in

```diff
   2 |+ #  The beast CMake configuration file
   3 |+ #
   4 |+ #             ** File generated automatically, do not modify **
```

> Username: vinniefalco  
> Created_at: 2016-10-10 16:10:43 UTC  
> Updated_at: 2016-10-14 09:49:45 UTC  
> Url: https://github.com/boostorg/beast/pull/110#discussion_r82634631  

What is this file? Does it go into the repository?

> Username: e-fominov  
> Created_at: 2016-10-11 08:15:45 UTC  
> Updated_at: 2016-10-14 09:49:45 UTC  
> Url: https://github.com/boostorg/beast/pull/110#discussion_r82743270  

yes, this this is template to generate CMake helper that will make CMake find Beast easily. It goes to repository. Similar other Boost library: https://github.com/boostorg/hana/tree/master/cmake


---

## Comment 7

> Username: vinniefalco  
> Created_at: 2016-10-11 12:58:18 UTC  
> Url: https://github.com/boostorg/beast/pull/110#issuecomment-252909115  

Part of #125 now

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2016-10-13 23:17:53 UTC  
> Url: https://github.com/boostorg/beast/pull/110#issuecomment-253666878  

This is having some build issues

---

## Comment 9

> Username: e-fominov  
> Created_at: 2016-10-14 07:36:37 UTC  
> Url: https://github.com/boostorg/beast/pull/110#issuecomment-253730434  

Fixed conflicts (merged master). Tested with MINGW and MSVC, boost 1.62.0

---

## Comment 10

> Username: coveralls  
> Created_at: 2016-10-14 10:05:49 UTC  
> Url: https://github.com/boostorg/beast/pull/110#issuecomment-253760234  

[![Coverage Status](https://coveralls.io/builds/8335444/badge)](https://coveralls.io/builds/8335444)  
  
Coverage remained the same at 98.051% when pulling **8f1724a12a33ec94fd4b4a1c0d90034b5c97229a on e-fominov:cmake_scripts** into **325f579a1dabcb976a981018153ae4753b0a36d6 on vinniefalco:master**.

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2016-10-14 10:12:05 UTC  
> Url: https://github.com/boostorg/beast/pull/110#issuecomment-253761462  

What! How did regex sneak in?!

---

## Comment 12

> Username: e-fominov  
> Created_at: 2016-10-14 10:16:04 UTC  
> Url: https://github.com/boostorg/beast/pull/110#issuecomment-253762252  

it was required by ssl examples on my system to build with vs2015, so  regex was occasionaly added. now should work correct

---

## Review 13 [Changes requested]

> Username: ximinez  
> Created_at: 2016-10-20 00:50:34 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/beast/pull/110#pullrequestreview-4988091  

📁 CMakeLists.txt

```diff
  38 |+ find_package(Boost REQUIRED COMPONENTS coroutine context thread filesystem program_options system)
  39 |+ include_directories(${Boost_INCLUDE_DIRS})
  40 |+ link_directories(${Boost_LIBRARY_DIRS})
```

> Username: ximinez  
> Created_at: 2016-10-20 00:44:21 UTC  
> Updated_at: 2017-02-01 23:48:13 UTC  
> Url: https://github.com/boostorg/beast/pull/110#discussion_r84196128  

These Boost changes are causing my build all kinds of linker problems. Here are the first few errors:  
  
```  
$ cmake --build .  
libboost_filesystem-vc140-mt-s-1_62.lib(path_traits.obj) : error LNK2038: mismatch detected for '_ITERATOR_DEBUG_LEVEL': value '0' doesn't match value '2' in main.obj [C:\Dev\Beast\bin64\test\http\bench-tests.vcxproj]  
libboost_filesystem-vc140-mt-s-1_62.lib(path_traits.obj) : error LNK2038: mismatch detected for 'RuntimeLibrary': value 'MT_StaticRelease' doesn't match value 'MDd_DynamicDebug' in main.obj [C:\Dev\Beast\bin64\test\http\bench-tests.vcxproj]  
libboost_filesystem-vc140-mt-s-1_62.lib(path.obj) : error LNK2038: mismatch detected for '_ITERATOR_DEBUG_LEVEL': value '0' doesn't match value '2' in main.obj [C:\Dev\Beast\bin64\test\http\bench-tests.vcxproj]  
libboost_filesystem-vc140-mt-s-1_62.lib(path.obj) : error LNK2038: mismatch detected for 'RuntimeLibrary': value 'MT_StaticRelease' doesn't match value 'MDd_DynamicDebug' in main.obj [C:\Dev\Beast\bin64\test\http\bench-tests.vcxproj]  
libboost_filesystem-vc140-mt-s-1_62.lib(codecvt_error_category.obj) : error LNK2038: mismatch detected for '_ITERATOR_DEBUG_LEVEL': value '0' doesn't match value '2' in main.obj [C:\Dev\Beast\bin64\test\http\bench-tests.vcxproj]  
libboost_filesystem-vc140-mt-s-1_62.lib(codecvt_error_category.obj) : error LNK2038: mismatch detected for 'RuntimeLibrary': value 'MT_StaticRelease' doesn't match value 'MDd_DynamicDebug' in main.obj [C:\Dev\Beast\bin64\test\http\bench-tests.vcxproj]  
```  
  
I assume there's some difference between our build environments. To build boost, I unpacked the boost 1.62 tar file into `$BOOST_ROOT`, ran `bootstrap.bat`, then `./bjam.exe --toolset=msvc-14.0 address-model=64 stage`. That left me with the `stage` and `stage64` directories that this seems to be expecting.  
  
I have VS 14.0.25431.01 Update 3 and cmake 3.6.1 installed. I added some messages to the CMakeLists.txt to dump the Boost result variables:  
  
```  
$ cmake -G"Visual Studio 14 2015 Win64" -DCMAKE_INSTALL_PREFIX=c:\dev\lib ..      
[...]  
CMake Warning at C:/Program Files/CMake/share/cmake-3.6/Modules/FindBoost.cmake:743 (message):  
  Imported targets not available for Boost version 106200  
Call Stack (most recent call first):  
  C:/Program Files/CMake/share/cmake-3.6/Modules/FindBoost.cmake:842 (_Boost_COMPONENT_DEPENDENCIES)  
  C:/Program Files/CMake/share/cmake-3.6/Modules/FindBoost.cmake:1395 (_Boost_MISSING_DEPENDENCIES)  
  CMakeLists.txt:38 (find_package)  
[...]  
Boost_INCLUDE_DIRS: C:/boost_1_62_0  
Boost_LIBRARY_DIRS: C:/boost_1_62_0//stage64/lib  
Boost_LIBRARIES: C:/boost_1_62_0/stage64/lib/libboost_coroutine-vc140-mt-s-1_62.lib;C:/boost_1_62_0/stage64/lib/libboost_context-vc140-mt-s-1_62.lib;C:/boost_1_62_0/stage64/lib/libboost_thread-vc140-mt-s-1_62.lib;C:/boost_1_62_0/stage64/lib/libboost_filesystem-vc140-mt-s-1_62.lib;C:/boost_1_62_0/stage64/lib/libboost_program_options-vc140-mt-s-1_62.lib;C:/boost_1_62_0/stage64/lib/libboost_system-vc140-mt-s-1_62.lib  
```  
  
I don't see anything obviously incorrect there, though the warning raises flags.

> Username: ximinez  
> Created_at: 2016-10-20 00:50:29 UTC  
> Updated_at: 2017-02-01 23:48:13 UTC  
> Url: https://github.com/boostorg/beast/pull/110#discussion_r84196654  

I found a simple workaround: Don't include the `Boost_LIBRARIES` in Windows builds.  https://github.com/ximinez/Beast/commit/a5ef78d575af05feacc20050f8df7095e2c920e5  
  
How does that work for you?

> Username: vinniefalco  
> Created_at: 2016-10-20 01:01:54 UTC  
> Updated_at: 2017-02-01 23:48:13 UTC  
> Url: https://github.com/boostorg/beast/pull/110#discussion_r84197558  

hmm...if you have correctly installed Boost and/or OpenSSL on Windows you should not get the errors. Previous versions of the CMakeLists.txt (and also the one in rippled) were wrong.

> Username: e-fominov  
> Created_at: 2016-10-20 06:51:22 UTC  
> Updated_at: 2017-02-01 23:48:13 UTC  
> Url: https://github.com/boostorg/beast/pull/110#discussion_r84221523  

If you dont need to have two boosts - x64 and x86, its better to use bjam install.  
You have built your boost x64 into /stage folder, while cmake expects to have x86 in /stage and x64 in /stage64  
  
try this (after cleaning stage folders):  
  
```  
./bjam.exe --toolset=msvc-14.0 address-model=64 architecture=x86 link=static threading=multi runtime-link=shared stage --stagedir=stage64  
```

> Username: ximinez  
> Created_at: 2016-10-20 16:17:12 UTC  
> Updated_at: 2017-02-01 23:48:13 UTC  
> Url: https://github.com/boostorg/beast/pull/110#discussion_r84320490  

I'm trying this now. Will report back when it finishes. :clock2:

> Username: ximinez  
> Created_at: 2016-10-21 15:25:34 UTC  
> Updated_at: 2017-02-01 23:48:13 UTC  
> Url: https://github.com/boostorg/beast/pull/110#discussion_r84501985  

Sorry for the delay. This worked in every combination I was able to come up with. Additionally, @vinniefalco I was able to build boost using the rippled instructions in the same directory, and successfully build rippled in every combination I could come up with, too. It would probably be worthwhile to update the rippled builds and instructions to use this method, though, so we don't get drift.


---

## Comment 14

> Username: ximinez  
> Created_at: 2016-10-21 15:52:20 UTC  
> Url: https://github.com/boostorg/beast/pull/110#issuecomment-255414624  

:+1: However, it's now conflicting with b17 in `master`. Check me, but I went ahead and resolved the conflicts in https://github.com/ximinez/Beast/commits/ef-cmake_scripts.

---

## Comment 15

> Username: vinniefalco  
> Created_at: 2017-02-27 17:08:25 UTC  
> Url: https://github.com/boostorg/beast/pull/110#issuecomment-282783661  

I'm giving up on this

---
