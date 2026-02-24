# #37 - [Question] Is it possible to set the correct library type (STATIC/SHARED) when requesting a specific one? [Closed]

> Username: jpfeuffer  
> Created at: 2020-03-23 23:57:48 UTC  
> Updated at: 2020-05-06 19:21:38 UTC  
> Closed at: 2020-05-06 19:21:38 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37  

Hi,  
  
I am very new to the Config mode of the FindBoost module but I found that the library type of the created imported targets is (in all my builds so far) set to UNKNOWN (I suppose here: https://github.com/boostorg/boost_install/blob/develop/boost-install.jam#L915).  
  
Would it be possible to set this to the actual type so that our library and its target_link_library calls have this information? From what I understood it is important for target_link_library to know if a dependency is STATIC or SHARED since its logic depends on it.  
  
I think the old FindBoost.cmake way actually (at least for the specific request of Static libraries) sets this property correctly: https://github.com/Kitware/CMake/blob/master/Modules/FindBoost.cmake#L2218  
  
I understand that, especially for the FindBoost.cmake way, doing this correctly is not always easy (e.g. on Win where both types basically end with ".lib").   
  
But I am curious to know if this would be possible with the config approach. Or maybe it is possible and I am doing something wrong?

---

## Comment 1

> Username: pdimov  
> Created at: 2020-03-24 00:16:03 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-602928603  

Why is it important for `target_link_libraries` to know the library type? What logic depends on it?

---

## Comment 2

> Username: jpfeuffer  
> Created at: 2020-03-24 00:26:21 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-602931692  

It's about the transitivity of dependencies. I will try to summarize it correctly: If you have a shared library as a PRIVATE dependency in a `target_link_libraries` call (e.g. not exposed in your public API) another downstream library does not need to link to it, while for a static library it does.   
  
A nice explanation of the `target_link_libraries` logic:  
https://cmake.org/pipermail/cmake/2016-May/063400.html  
  
A related discussion on the CMake issue tracker, where someone seems to have a similar problem:  
https://gitlab.kitware.com/cmake/cmake/issues/20483

---

## Comment 3

> Username: pdimov  
> Created at: 2020-03-24 00:48:18 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-602938287  

OK, and is this causing you a specific problem?

---

## Comment 4

> Username: pdimov  
> Created at: 2020-03-24 04:11:22 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-603003323  

To answer your question: yes, it's possible, although it would require some reorganization. The target is currently created before the library type is known, which is why I use UNKNOWN. Target creation needs to be deferred until the type is known.  
  
I'm looking into this.

---

## Comment 5

> Username: jpfeuffer  
> Created at: 2020-03-24 11:38:42 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-603189427  

First of all, thanks a lot, I think it would ease the exposure of boost as a dependency for downstream code.  
  
The current workaround for us is to intercept if the user set Boost_USE_STATIC while configuring our library and to implement similar logic as `target_link_libraries` in our own CMake scripts. If Boost_USE_STATIC is OFF we currently assume that a shared library was found (although this is not always true and we would actually need to implement a fallback check as suggested in the CMake issue; depending on the library suffix etc.)  
  
So, it is not a huge blocker but would ease the process a lot.

---

## Comment 6

> Username: pdimov  
> Created at: 2020-03-24 13:03:07 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-603225534  

If you can test a patch to `boost-install.jam`, this is it: https://github.com/boostorg/boost_install/commit/bdade997ccbb359ba54400c9689bd571408b4967

---

## Comment 7

> Username: jpfeuffer  
> Created at: 2020-03-24 15:28:44 UTC  
> Updated at: 2020-03-24 15:46:15 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-603306743  

Cool. I am on it. It works for static, building shared now.  
EDIT: Looks good in shared mode, too.  
  
I am only testing on macOS and only build regex, math, system, thread, iostreams. But there it looks great for me.

---

## Comment 8

> Username: pdimov  
> Created at: 2020-03-24 16:56:55 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-603367955  

I tried to add a test for this issue with https://github.com/boostorg/boost_install/commit/f93818c02d19f16a33364891d528ca3aeef06bba, but it passes: https://travis-ci.org/github/boostorg/boost_install/jobs/666369665  
  
Any idea what I need to do to make it fail? (This branch does not contain the fix.)

---

## Comment 9

> Username: jpfeuffer  
> Created at: 2020-03-24 17:24:52 UTC  
> Updated at: 2020-03-24 17:29:35 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-603387501  

Can you try to change the function get_random_number() to have a boost type in its (public) interface (i.e. in its return value or its parameters)? You could pass the device for example.  
EDIT: You might need to add a header file to the library, so the main executable will have the boost random include as well. CMake should then figure out, that using this library with boost in its public interface, will need the includes and the link command to boost random. With PRIVATE and a shared boost random library it should not work then.

---

## Comment 10

> Username: pdimov  
> Created at: 2020-03-24 17:36:45 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-603396285  

If `main.cpp` uses a type from Boost.Random, it will have to link to Boost::random. Otherwise (in principle) Boost.Random's headers defining the type won't be in the include path.  
  
(This is not true for Boost at the moment because all the headers are always available, but this just allows incorrect target descriptions to compile, it doesn't make them correct.)

---

## Comment 11

> Username: pdimov  
> Created at: 2020-03-24 17:37:36 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-603396809  

(In other words, it won't be correct for `lib` to link privately to Boost::random, if it uses a Boost.Random type in its public interface.)

---

## Comment 12

> Username: jpfeuffer  
> Created at: 2020-03-24 17:43:46 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-603400980  

Sorry, you are right. This is not the way to go for testing this. Let me think about a test a bit more.

---

## Comment 13

> Username: bradking  
> Created at: 2020-03-24 18:58:24 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-603446102  

@pdimov the use case in question is when an executable uses a shared library that itself has a private dependency on another shared library.  The latter must be in a different directory that is not searched by the linker by default when searching for dependencies of encountered shared libraries.  CMake will automatically generate the proper `-rpath-link` flag to help the linker find the dependency.  In your test you need the intermediate `lib` to be `SHARED`.

---

## Comment 14

> Username: pdimov  
> Created at: 2020-03-24 19:10:06 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-603451634  

That's what my test does. `lib` is a shared library (when BUILD_SHARED_LIBS=ON) that has a private dependency on another shared library, Boost::random (libboost_random.so.1.73.0). See  
  
https://travis-ci.org/github/boostorg/boost_install/jobs/666369665#L5220  
https://travis-ci.org/github/boostorg/boost_install/jobs/666369665#L5241

---

## Comment 15

> Username: bradking  
> Created at: 2020-03-24 19:18:34 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-603455420  

In the test it looks like `LD_LIBRARY_PATH` is set to the location of `libboost_random.so`.  That will let the linker find what it needs with no help from CMake.  
  
We don't necessarily have to directly test the motivating use case for this.  CMake already has its own tests.  You could just use `get_property` to get the [TYPE](https://cmake.org/cmake/help/v3.17/prop_tgt/TYPE.html) target property on the imported target and verify that it is `SHARED` (or `STATIC)` and not `UNKNOWN`.

---

## Comment 16

> Username: pdimov  
> Created at: 2020-03-24 22:23:47 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-603536196  

`LD_LIBRARY_PATH` is set after `cmake --build .` and before `cmake --build . --target check`, which just runs `ctest`. The linker doesn't run under `LD_LIBRARY_PATH` set. But I've fired off a job with `LD_LIBRARY_PATH` not set at all to check this.

---

## Comment 17

> Username: pdimov  
> Created at: 2020-03-25 12:38:50 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-603815948  

Removing LD_LIBRARY_PATH had no effect on the `test/random` test.  
  
It did cause a (run) failure in `test/mpi`, which is caused by libboost_mpi.so privately using libboost_serialization.so; but this failure is not fixed by using SHARED IMPORTED instead of UNKNOWN IMPORTED.  
  
However, the Travis tests use CMake 3.12, and I think I remember rpath changes in a later CMake (3.16?) where using the correct type may help.

---

## Comment 18

> Username: pdimov  
> Created at: 2020-03-26 13:36:39 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-604435210  

I reran the test with CMake 3.16, and the results didn't change.  
  
UNKNOWN IMPORTED: https://travis-ci.org/github/boostorg/boost_install/builds/667027727  
STATIC/SHARED IMPORTED: https://travis-ci.org/github/boostorg/boost_install/builds/667029047

---

## Comment 19

> Username: bradking  
> Created at: 2020-03-26 13:54:28 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-604444172  

Please try to get the output of `readelf  -d` on the `liblib.so` library file in the test.  It would also be nice to get `make VERBOSE=1` output from the build steps.  
  
Also please extend the test to directly verify the `TYPE` of `Boost::random` as `SHARED` (or `STATIC`).  That should be done regardless of whether we can get the CI build to reproduce the motivating use case.  
  
The `UNKNOWN` library type in CMake is meant *only* for use on an imported target created from a `find_library` result because we don't know what kind of file will be specified in the corresponding cache entry by the user.  In any situation where the imported target comes with a file in the same distribution as the library, the imported target should know the exact type.

---

## Comment 20

> Username: pdimov  
> Created at: 2020-03-26 14:43:50 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-604470932  

Thanks Brad for the help. Here are the results (with UNKNOWN): https://travis-ci.com/github/pdimov/boost_install/builds/155548956  
```  
-- Boost::random: UNKNOWN_LIBRARY IMPORTED debug 'imploc_d-NOTFOUND' release '/home/travis/.local/lib/libboost_random.so.1.73.0'  
```  
readelf on liblib.so:  
```  
 0x000000000000000f (RPATH)              Library rpath: [/home/travis/.local/lib]  
```  
readelf on main:  
```  
 0x000000000000000f (RPATH)              Library rpath: [/home/travis/build/pdimov/boost-root/tools/boost_install/test/random/__build__]  
```  
Link line:  
```  
/usr/bin/g++ -fPIC   -shared -Wl,-soname,liblib.so -o liblib.so CMakeFiles/lib.dir/lib.cpp.o  -Wl,-rpath,/home/travis/.local/lib /home/travis/.local/lib/libboost_random.so.1.73.0 /home/travis/.local/lib/libboost_system.so.1.73.0   
```  
Everything seems correct here.

---

## Comment 21

> Username: bradking  
> Created at: 2020-03-26 15:04:33 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-604482492  

@pdimov the RPATH on `main` includes the path to the boost library so that becomes sufficient for the linker to follow transitive dependencies through `liblib.so`.  That's why the test passes with `UNKNOWN`.  The use case where this came up is when `liblib.so` has a fully private dependency in a path that `main` knows nothing about.

---

## Comment 22

> Username: pdimov  
> Created at: 2020-03-26 15:13:04 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-604487340  

I don't see RPATH on main containing the path to the boost library. It only has the path to liblib.so.

---

## Comment 23

> Username: bradking  
> Created at: 2020-03-26 15:28:26 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-604495994  

Oops, I mixed up which path contains what.  That liblib's rpath contains the path to its dependency is enough for the linker to find it.  Actually I'm not sure how the rpath is getting there when the dependency is `UNKNOWN`.  What does the complete imported target look like?

---

## Comment 24

> Username: pdimov  
> Created at: 2020-03-26 15:36:40 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-604500788  

```  
add_library(Boost::random UNKNOWN IMPORTED)  
  
set_target_properties(Boost::random PROPERTIES  
  INTERFACE_INCLUDE_DIRECTORIES "${_BOOST_INCLUDEDIR}"  
  INTERFACE_COMPILE_DEFINITIONS "BOOST_ALL_NO_LIB"  
)  
```  
```  
set_property(TARGET Boost::random APPEND PROPERTY IMPORTED_CONFIGURATIONS RELEASE)  
  
set_target_properties(Boost::random PROPERTIES  
  IMPORTED_LINK_INTERFACE_LANGUAGES_RELEASE CXX  
  IMPORTED_LOCATION_RELEASE "${_BOOST_LIBDIR}/libboost_random.so.1.73.0"  
  )  
  
set_target_properties(Boost::random PROPERTIES  
  MAP_IMPORTED_CONFIG_MINSIZEREL Release  
  MAP_IMPORTED_CONFIG_RELWITHDEBINFO Release  
  )  
  
set_property(TARGET Boost::random APPEND  
  PROPERTY INTERFACE_COMPILE_DEFINITIONS "BOOST_RANDOM_DYN_LINK"  
  )  
```

---

## Comment 25

> Username: bradking  
> Created at: 2020-03-26 17:51:25 UTC  
> Url: https://github.com/boostorg/boost_install/issues/37#issuecomment-604580492  

Okay, I finally got to the bottom of this.  CMake does have [code here](https://gitlab.kitware.com/cmake/cmake/-/blob/v3.17.0/Source/cmComputeLinkInformation.cxx#L1640-1645) to support adding RPATH entries for shared libraries linked via `UNKNOWN` imported targets.  It works by inspecting the actual file path that is imported.  That's why your test case passes.  
  
The full use case in [CMake Issue 20483](https://gitlab.kitware.com/cmake/cmake/issues/20483) is actually when the imported target is a shared library that itself privately depends on other shared libraries.  The `IMPORTED_LINK_DEPENDENT_LIBRARIES` target property on an imported `SHARED` library will communicate that dependency, but it is not used for `UNKNOWN` libraries.  For example:  
  
```cmake  
add_library(A SHARED IMPORTED)  
set_target_properties(A PROPERTIES  
  IMPORTED_LOCATION /path/to/A/liba.so  
  )  
  
add_library(B SHARED IMPORTED)  
set_target_properties(B PROPERTIES  
  IMPORTED_LOCATION /path/to/B/libb.so  
  IMPORTED_LINK_DEPENDENT_LIBRARIES A # libb.so links to liba.so privately  
  )  
```  
  
Now if something links to `libb.so` then the linker needs to be able to locate `liba.so`.  CMake adds the proper `-Wl,-rpath-link,...` flag for that.  However, if imported target `B` is `UNKNOWN` instead then CMake doesn't know it should check for `IMPORTED_LINK_DEPENDENT_LIBRARIES`.  
  
Boost should be updated both to set the proper type on the imported library targets and to set `IMPORTED_LINK_DEPENDENT_LIBRARIES` for any private dependencies (referring to their imported targets).
