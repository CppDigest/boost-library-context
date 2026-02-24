# #27 [CMake] Add minimal cmake file [Merged]

> Username: Mike-Devel  
> Created at: 2019-01-02 21:56:34 UTC  
> Updated at: 2019-01-05 07:05:10 UTC  
> Merged at: 2019-01-05 00:03:25 UTC  
> Closed at: 2019-01-05 00:03:25 UTC  
> Url: https://github.com/boostorg/pool/pull/27  

This cmake file just provides the minimal infrastructure necessary, such that other libraries can get a sensible result from calling  
  
     add_subdirectory( <path-to-boost_pool> )  
     target_link_library( <my_lib> PUBLIC Boost::pool )  
  
More information:  
  
- https://groups.google.com/forum/#!topic/boost-developers-archive/kM4JRmyVl3M%5B1-25%5D  
- https://groups.google.com/d/msg/boost-developers-archive/4HU-RzReL7U/FS1X6OFrEQAJ  
  
Of course the file can be extended to e.g. build and run tests and support installation, but that is out of scope for this particular PR.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-01-03 07:55:44 UTC  
> Updated_at: 2019-01-03 07:58:30 UTC  
> Url: https://github.com/boostorg/pool/pull/27#issuecomment-451074758  

# [Codecov](https://codecov.io/gh/boostorg/pool/pull/27?src=pr&el=h1) Report  
> Merging [#27](https://codecov.io/gh/boostorg/pool/pull/27?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/pool/commit/e4b44b276ca6c68ccf9eac4232157340b699453c?src=pr&el=desc) will **decrease** coverage by `0.22%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/pool/pull/27/graphs/tree.svg?width=650&token=blmk8CZxg2&height=150&src=pr)](https://codecov.io/gh/boostorg/pool/pull/27?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #27      +/-   ##  
===========================================  
- Coverage    71.62%   71.39%   -0.23%       
===========================================  
  Files            8        8                
  Lines          444      444                
  Branches       132      132                
===========================================  
- Hits           318      317       -1       
- Misses          29       30       +1       
  Partials        97       97  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/pool/pull/27?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/pool/object\_pool.hpp](https://codecov.io/gh/boostorg/pool/pull/27/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wb29sL29iamVjdF9wb29sLmhwcA==) | `72.97% <0%> (-2.71%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/pool/pull/27?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/pool/pull/27?src=pr&el=footer). Last update [e4b44b2...71179a2](https://codecov.io/gh/boostorg/pool/pull/27?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2019-01-04 18:11:11 UTC  
> Url: https://github.com/boostorg/pool/pull/27#issuecomment-451523014  

Can someone explain to me, what codecov is complaining about?

---

## Comment 3

> Username: jeking3  
> Created_at: 2019-01-04 18:15:12 UTC  
> Url: https://github.com/boostorg/pool/pull/27#issuecomment-451523981  

Don't worry about it.

---

## Comment 4

> Username: jeking3  
> Created_at: 2019-01-04 18:32:56 UTC  
> Url: https://github.com/boostorg/pool/pull/27#issuecomment-451528631  

I created a new directory C:\Boost\libs\foo and populated it with two files:  
CMakeLists.txt:  
```  
cmake_minimum_required(VERSION 3.3)  
add_subdirectory(C:/boost/libs/pool)  
add_executable(empty empty.cpp)  
target_link_library(empty PUBLIC Boost::pool)  
```  
empty.cpp:  
```  
#include "boost/pool/pool_alloc.hpp"  
  
int main()  
{  
    return 0;  
}  
```  
  
I created an out-of-tree directory and tried to run cmake:  
```  
c:\boost\libs>mkdir foo-build  
  
c:\boost\libs>cd foo-build  
  
c:\boost\libs\foo-build>cmake ..\foo  
-- Building for: Visual Studio 15 2017  
-- The C compiler identification is MSVC 19.16.27025.1  
-- The CXX compiler identification is MSVC 19.16.27025.1  
-- Check for working C compiler: C:/Program Files (x86)/Microsoft Visual Studio/2017/Community/VC/Tools/MSVC/14.16.27023/bin/Hostx86/x86/cl.exe  
-- Check for working C compiler: C:/Program Files (x86)/Microsoft Visual Studio/2017/Community/VC/Tools/MSVC/14.16.27023/bin/Hostx86/x86/cl.exe -- works  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Detecting C compile features  
-- Detecting C compile features - done  
-- Check for working CXX compiler: C:/Program Files (x86)/Microsoft Visual Studio/2017/Community/VC/Tools/MSVC/14.16.27023/bin/Hostx86/x86/cl.exe  
-- Check for working CXX compiler: C:/Program Files (x86)/Microsoft Visual Studio/2017/Community/VC/Tools/MSVC/14.16.27023/bin/Hostx86/x86/cl.exe -- works  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
CMake Error at CMakeLists.txt:2 (add_subdirectory):  
  add_subdirectory not given a binary directory but the given source  
  directory "C:/boost/libs/pool" is not a subdirectory of  
  "C:/boost/libs/foo".  When specifying an out-of-tree source a binary  
  directory must be explicitly specified.  
  
  
CMake Error at CMakeLists.txt:4 (target_link_library):  
  Unknown CMake command "target_link_library".  
  
  
-- Configuring incomplete, errors occurred!  
See also "C:/boost/libs/foo-build/CMakeFiles/CMakeOutput.log".  
  
c:\boost\libs\foo-build>  
```  
  
I'm sure I did something wrong, but I expected it to work based on your pull request comment content.  The only file checked in here is the CMakeLists.txt file and I am unaware of any directives to copy the CMakeLists.txt file to a directory that would be usable after constructing a deliverable?

---

## Comment 5

> Username: jeking3  
> Created_at: 2019-01-04 18:35:16 UTC  
> Url: https://github.com/boostorg/pool/pull/27#issuecomment-451529208  

When I placed a copy of C:\Boost\libs\pool into C:\Boost\libs\foo\pool and adjusted the CMakeLists.txt file, it got past the first issue, but still complains that the command target_link_library is an unknown command?

---

## Comment 6

> Username: jeking3  
> Created_at: 2019-01-04 18:41:17 UTC  
> Url: https://github.com/boostorg/pool/pull/27#issuecomment-451530848  

After I fixed the command from the PR comment to "target_link_libraries" I got further:  
```  
c:\boost\libs\foo-build>cmake ..\foo  
-- Configuring done  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::assert" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::config" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::integer" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::throw_exception" but the target was  
  not found.  Perhaps a find_package() call is missing for an IMPORTED  
  target, or an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::type_traits" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::winapi" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::assert" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::config" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::integer" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::throw_exception" but the target was  
  not found.  Perhaps a find_package() call is missing for an IMPORTED  
  target, or an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::type_traits" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::winapi" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::assert" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::config" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::integer" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::throw_exception" but the target was  
  not found.  Perhaps a find_package() call is missing for an IMPORTED  
  target, or an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::type_traits" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::winapi" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::assert" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::config" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::integer" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::throw_exception" but the target was  
  not found.  Perhaps a find_package() call is missing for an IMPORTED  
  target, or an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::type_traits" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::winapi" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::assert" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::config" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::integer" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::throw_exception" but the target was  
  not found.  Perhaps a find_package() call is missing for an IMPORTED  
  target, or an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::type_traits" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at CMakeLists.txt:3 (add_executable):  
  Target "empty" links to target "Boost::winapi" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
-- Generating done  
-- Build files have been written to: C:/boost/libs/foo-build  
```  
  
Okay, apart from some significant error repetition, it's telling me that I have to add some subdirectories.  Will the order of those subdirectories matter?  Once I pull in those subdirectories, I will have to keep pulling in libraries they depend on until I've pulled in all the dependencies, correct?  
  
I don't see how this makes boost most usable than using a versioned release and the FindBoost cmake module.

---

## Comment 7

> Username: jeking3  
> Created_at: 2019-01-04 18:42:34 UTC  
> Url: https://github.com/boostorg/pool/pull/27#issuecomment-451531255  

FYI I'll merge this if everyone else is going along with it.  I assume it's just a first step in a direction...

---

## Comment 8

> Username: Mike-Devel  
> Created_at: 2019-01-05 07:04:03 UTC  
> Updated_at: 2019-01-05 07:05:10 UTC  
> Url: https://github.com/boostorg/pool/pull/27#issuecomment-451633691  

@jeking3: First of all, thanks for the merge and sorry for the typo ( `target_link_library` vs `target_link_libraries` ). I created another PR (https://github.com/boostorg/pool/pull/28) that will use the cmake file in a dummy project similar to what you did. That should help clarify the inteded usage. I don't know how to hook it up to your CI-script though.   
  
> Will the order of those subdirectories matter?  
  
No  
  
>  Once I pull in those subdirectories, I will have to keep pulling in libraries they depend on until I've pulled in all the dependencies, correct?  
  
Yes. One could e.g. use peter dimov's depinst.py script for that and I assume you are doing something similar in your ci builds already.  
  
> I don't see how this makes boost most usable than using a versioned release and the FindBoost cmake module.  
  
It has different advantages and disadvantages.   
- First of all, it is (currently) more fine-granular than the classic approach. It doesn't require the download of a full release or the use of the boost superproject at all - just the bits I'm interested in. And I can also  express exectly which boost libraries I depend on instead of just "all header-only boost libraries" (`target_link_libraries( my_app Boost::pool Boost::circular_buffer )` vs `target_link_libraries( my_app Boost::boost )` .  
- Second, for compiled libraries it makes it much easier to compile the whole project with a consistent set of flags: I already have cmake toolchain files for my different cross-platform targets, with/without sanitizers, special optimizer settings,.... Replicating all of that into a b2 build can be pretty annoying to say the least.  
  
- Now, If I do want to use the majority of boost, I can still just do a `git clone --recursive https://github.com/boostorg/boost.git`, add a cmake file like the one proposed in https://github.com/boostorg/boost/pull/193 into the root directory and have a single `add_subdirectory( <path-to-boost-root> )` in my project's own cmake file.  
  
Disadvantages I see are:  
- Time to hello world is longer (assuming you already know how to use b2)  
- You are not sharing build artifacts between different projects - each one compiles boost as part of its own build process  
- Requires more work from the maintainers (in particular the maintainers of header-only libraries)  
   
> I assume it's just a first step in a direction...  
  
More or less. From here I see two possible ways forward:   
1. CMake support remains a per-project thing and each maintainer can decide how much additional functionality he/she wants to put into the cmake file (e.g. also compile unit-tests) and how it is implemented and what tools are used to test and maintain the files (I.e. keep list of dependencies up to date).  
2. Somebody manages to bring an official, full fledged cmake solution to boost (such as Robert's most recent effort). That will most likely mean that some sort of support library will be added to boost (e.g. something like https://github.com/boost-cmake/bcm) this will result in a more uniform support, probably simpler individual cmake scripts and eventually maybe the total replacement of b2.  
  
Note however, that I personally will not be involved in either of those efforts (at least not for boost libraries that I don't use directly myself).

---
