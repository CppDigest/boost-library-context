# #95 - Boost CMake testing procedure fails for Lockfree [Closed]

> Username: pdimov  
> Created at: 2023-12-17 00:52:56 UTC  
> Updated at: 2023-12-18 15:50:30 UTC  
> Closed at: 2023-12-17 07:12:29 UTC  
> Url: https://github.com/boostorg/lockfree/issues/95  

The [documented testing procedure](https://github.com/boostorg/cmake#testing-boost-with-cmake)  
  
```  
mkdir __build && cd __build  
cmake -DBUILD_TESTING=ON -DBOOST_INCLUDE_LIBRARIES=lockfree ..  
cmake --build . --target tests  
ctest --output-on-failure --no-tests=error  
```  
  
fails for Boost.Lockfree, because the test executables aren't built by the target `tests`. BoostTest handles this automatically, but for tests declared manually, one needs to first declare the `tests` target if not present  
  
```  
if(NOT TARGET tests)  
    add_custom_target(tests)  
endif()  
```  
  
and then for each test executable, use `add_dependencies(test my_test_executable)`.  
  
Or, since there is already a target `boost_lockfree_all_tests`, it should be enough to make it a dependency of `tests`: `add_dependencies(tests boost_lockfree_all_tests)`.  
  
(For this to keep working after the eventual fix, it might be a good idea to add it to CI.)  
  
It's also good practice to make the test executables EXCLUDE_FOR_ALL, so that building the `tests` target builds them, but building the default target doesn't, but this step is not in principle required.

---

## Comment 1

> Username: timblechmann  
> Created at: 2023-12-17 00:56:51 UTC  
> Url: https://github.com/boostorg/lockfree/issues/95#issuecomment-1859002100  

i'll have a look  
  
> For this to keep working after the eventual fix, it might be a good idea to add it to CI  
  
do you have any link at hand that tests the cmake build of a boost library on CI?

---

## Comment 2

> Username: pdimov  
> Created at: 2023-12-17 01:07:30 UTC  
> Url: https://github.com/boostorg/lockfree/issues/95#issuecomment-1859003974  

Any of my libraries, for example Function: https://github.com/boostorg/function/blob/develop/.github/workflows/ci.yml  
  
`posix-cmake-subdir` and `windows-cmake-subdir` test whether the library can be used with `add_subdirectory` (and indirectly, with `FetchContent`.)  
  
`posix-cmake-install` and `windows-cmake-install` test whether the library can be used after being installed with CMake.  
  
`posix-cmake-test` and `windows-cmake-test` run the library tests with CMake.

---

## Comment 3

> Username: timblechmann  
> Created at: 2023-12-17 02:21:51 UTC  
> Url: https://github.com/boostorg/lockfree/issues/95#issuecomment-1859017108  

@pdimov, i'd appreciate a second pair of eyes: the `cmake-install` targets don't seem to pick up the boost.lockfree headers. it seems that the `install` step doesn't install them: https://github.com/boostorg/lockfree/actions/runs/7235711376/job/19713463190  
  
any idea what's wrong here?  
https://github.com/boostorg/lockfree/pull/96/files

---

## Comment 4

> Username: pdimov  
> Created at: 2023-12-17 02:41:08 UTC  
> Url: https://github.com/boostorg/lockfree/issues/95#issuecomment-1859019633  

Interesting. Try adding `-DBoost_DEBUG=ON` to the `cmake` command line, it might tell us why the installation support for Lockfree doesn't activate.

---

## Comment 5

> Username: timblechmann  
> Created at: 2023-12-17 02:53:52 UTC  
> Url: https://github.com/boostorg/lockfree/issues/95#issuecomment-1859021261  

```  
-- Adding Boost library lockfree  
-- Not enabling installation for 'lockfree'; interface include directory '$<BUILD_INTERFACE:/home/runner/work/lockfree/boost-root/libs/lockfree>;/home/runner/work/lockfree/boost-root/libs/lockfree/include' does not equal '/home/runner/work/lockfree/boost-root/libs/lockfree/include'  
```  
  
https://github.com/boostorg/lockfree/actions/runs/7235908968/job/19713877508  
  
🤔

---

## Comment 6

> Username: timblechmann  
> Created at: 2023-12-17 03:01:16 UTC  
> Url: https://github.com/boostorg/lockfree/issues/95#issuecomment-1859022142  

the offender is:  
```  
target_sources(boost_lockfree PUBLIC FILE_SET HEADERS FILES ${Headers} )  
```  
  
this is quite unfortunate, as `FILES_SET` seems to be the current cmake way to generate IDE projects for header-only libraries

---

## Comment 7

> Username: pdimov  
> Created at: 2023-12-17 03:01:52 UTC  
> Url: https://github.com/boostorg/lockfree/issues/95#issuecomment-1859022236  

> Target properties related to include directories are also modified by target_sources(FILE_SET) as follows:  
  
>   
> [INCLUDE_DIRECTORIES](https://cmake.org/cmake/help/latest/prop_tgt/INCLUDE_DIRECTORIES.html#prop_tgt:INCLUDE_DIRECTORIES)  
  
>   
> If the TYPE is HEADERS, and the scope of the file set is PRIVATE or PUBLIC, all of the BASE_DIRS of the file set are wrapped in [$<BUILD_INTERFACE>](https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html#genex:BUILD_INTERFACE) and appended to this property.  
  
>   
> [INTERFACE_INCLUDE_DIRECTORIES](https://cmake.org/cmake/help/latest/prop_tgt/INTERFACE_INCLUDE_DIRECTORIES.html#prop_tgt:INTERFACE_INCLUDE_DIRECTORIES)  
  
>   
> If the TYPE is HEADERS, and the scope of the file set is INTERFACE or PUBLIC, all of the BASE_DIRS of the file set are wrapped in [$<BUILD_INTERFACE>](https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html#genex:BUILD_INTERFACE) and appended to this property.

---

## Comment 8

> Username: pdimov  
> Created at: 2023-12-17 03:06:54 UTC  
> Url: https://github.com/boostorg/lockfree/issues/95#issuecomment-1859023111  

I was wondering whether the FILE_SET will also install the headers, and if so, where exactly will they go, because you're listing them with the `include/` in front. But it doesn't look like they are being installed anywhere, which is kind of what we want.  
  
I suppose the easiest way to patch this is to clear the INTERFACE_INCLUDE_DIRECTORIES property after `target_sources`.  
  
(In either case I think that your base directory is wrong, it should be `include` and not the root, because you want to include the headers as `<boost/something>`, not `<include/boost/something>`.)  
  
In principle, I can modify BoostInstall to also support `<$BUILD_INTERFACE:include>` in addition to just `include`, but that's not going to simplify things much on your side.

---

## Comment 9

> Username: timblechmann  
> Created at: 2023-12-17 03:13:16 UTC  
> Url: https://github.com/boostorg/lockfree/issues/95#issuecomment-1859024275  

in the long-term it would be great of `BoostInstall` could support `FILE_SET`. for the time being, i'll try to muscle the target property back in

---

## Comment 10

> Username: pdimov  
> Created at: 2023-12-17 03:34:12 UTC  
> Url: https://github.com/boostorg/lockfree/issues/95#issuecomment-1859027367  

```  
target_sources(boost_lockfree PUBLIC FILE_SET HEADERS FILES ${Headers} )  
```  
This should probably be INTERFACE since your library is header-only?

---

## Comment 11

> Username: timblechmann  
> Created at: 2023-12-17 07:12:29 UTC  
> Url: https://github.com/boostorg/lockfree/issues/95#issuecomment-1859058278  

#96 should fix it

---

## Comment 12

> Username: pdimov  
> Created at: 2023-12-17 14:40:48 UTC  
> Url: https://github.com/boostorg/lockfree/issues/95#issuecomment-1859190970  

The superproject now also accepts `${CMAKE_SOURCE_DIR}/include` wrapped with `$<BUILD_INTERFACE>`: https://github.com/boostorg/cmake/commit/e388dba756805ea80df5eec7a66e887dc271b278  
  
So you can in principle change it to  
  
```  
if (CMAKE_VERSION VERSION_GREATER_EQUAL 3.23)  
    set(Headers  
        include/boost/lockfree/spsc_queue.hpp  
        include/boost/lockfree/policies.hpp  
        include/boost/lockfree/queue.hpp  
        include/boost/lockfree/lockfree_forward.hpp  
        include/boost/lockfree/detail/prefix.hpp  
        include/boost/lockfree/detail/copy_payload.hpp  
        include/boost/lockfree/detail/tagged_ptr_dcas.hpp  
        include/boost/lockfree/detail/tagged_ptr.hpp  
        include/boost/lockfree/detail/tagged_ptr_ptrcompression.hpp  
        include/boost/lockfree/detail/atomic.hpp  
        include/boost/lockfree/detail/freelist.hpp  
        include/boost/lockfree/detail/parameter.hpp  
        include/boost/lockfree/stack.hpp  
    )  
  
    target_sources(boost_lockfree INTERFACE FILE_SET HEADERS BASE_DIRS include FILES ${Headers} )  
else()  
    target_include_directories(boost_lockfree INTERFACE include)  
endif()  
```  
and it should work. Not sure whether PUBLIC would work too; the private property only applies when building your library and it doesn't require any building, but maybe you still need that for the IDE?

---

## Comment 13

> Username: timblechmann  
> Created at: 2023-12-18 06:37:06 UTC  
> Url: https://github.com/boostorg/lockfree/issues/95#issuecomment-1859638884  

many thanks for looking into it: unfortunately it doesn't seem to work with `target_sources(... PUBLIC ...)`: https://github.com/boostorg/lockfree/actions/runs/7244468272/job/19732844329?pr=98  
  
if i'm using `INTERFACE` instead of `PUBLIC`, qtcreator doesn't generate an IDE target for header-only libraries.

---

## Comment 14

> Username: pdimov  
> Created at: 2023-12-18 15:50:29 UTC  
> Url: https://github.com/boostorg/lockfree/issues/95#issuecomment-1860871253  

That's because you are not setting the base directory to `include`, so your property gets set to `${CMAKE_SOURCE_DIR}`, instead of `${CMAKE_SOURCE_DIR}/include`.  
  
As I mentioned before, that's not going to be correct because the headers are included as `<boost/header.hpp>` and not `<include/boost/header.hpp>`. You want `${CMAKE_SOURCE_DIR}/include` in the `-I` path and not just `${CMAKE_SOURCE_DIR}`.  
  
You need to add `BASE_DIRS include` to the `target_sources` command.
