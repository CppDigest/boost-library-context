# #41 - Cannot find target Boost::boost after add_subdirectory [Closed]

> Username: q-ycong-p  
> Created at: 2023-07-31 22:03:43 UTC  
> Updated at: 2023-08-02 19:04:31 UTC  
> Closed at: 2023-08-02 19:04:31 UTC  
> Url: https://github.com/boostorg/cmake/issues/41  

My project git checks out Boost's source code to a subdirectory, and adds it through `add_subdirectory`. I'd like to use `Boost::boost` in my main project via `target_link_libraries(myTarget INTERFACE Boost::boost)`. However `Boost::boost` is not recognized while the other individual boost libraries work fine.  
  
I looked into `config/BoostConfig.cmake` and found that `Boost::boost` is added as a target [here](https://github.com/boostorg/cmake/blob/69f16e2819c4686450bdac807e3eb58346fb2ac2/config/BoostConfig.cmake#L134) - however BoostConfig.cmake is NOT installed if `BOOST_SKIP_INSTALL_RULES` or `BOOST_SKIP_INSTALL_RULES` - in this case is true because I am not installing Boost as project root, but rather added as a subdirectory.  
  
It seems that making `Boost::boost` available in the `add_subdirectory` use case would be a correct solution. Can someone (1) help me understand what's the correct way I can workaround it? And (2) Would be make sense to open this as a feature request, or do I miss the intended usage here?

---

## Comment 1

> Username: pdimov  
> Created at: 2023-07-31 22:14:57 UTC  
> Url: https://github.com/boostorg/cmake/issues/41#issuecomment-1659262308  

`Boost::boost` is a bit of a fake target, because it relies on the fact that after installation, all Boost headers are in the same directory (which it can then add to the include path.)  
  
However, with `add_subdirectory` use, the headers aren't in the same directory, but in their corresponding `libs/<libname>/include` directories. So providing a `Boost::boost` target in this case would necessitate adding all those directories (for each Boost library). It can be done, but I'm not sure why you would want that instead of linking to the corresponding library targets.

---

## Comment 2

> Username: q-ycong-p  
> Created at: 2023-07-31 23:37:13 UTC  
> Url: https://github.com/boostorg/cmake/issues/41#issuecomment-1659350613  

Thanks @pdimov!  
  
In my project's native build system, Boost is already installed, and `Boost::boost` is available to link. In a cross-compilation scenario, the project downloads Boost from source to re-compile, uses this `cmake` submodule to add Boost via `add_subdirectory`. The need for `Boost::boost` is in hope to unify the project's cmake logic.  
  
What would you suggest as the best way for my use case? Would I best use some if-else logic and explicitly link to individual Boost libraries if detect cross-compilation? Ty!

---

## Comment 3

> Username: pdimov  
> Created at: 2023-08-01 08:38:16 UTC  
> Url: https://github.com/boostorg/cmake/issues/41#issuecomment-1659840318  

I think that you can create the target `Boost::boost` yourself in the `add_subdirectory` path, by using something like  
```  
  add_library(Boost::boost INTERFACE IMPORTED)  
  set_property(TARGET Boost::boost APPEND PROPERTY INTERFACE_LINK_LIBRARIES Boost::lib1 Boost::lib2)  
```  
where `lib1` and `lib2` are the (header-only) Boost libraries your project needs to use.

---

## Comment 4

> Username: q-ycong-p  
> Created at: 2023-08-01 16:29:48 UTC  
> Updated at: 2023-08-01 16:48:13 UTC  
> Url: https://github.com/boostorg/cmake/issues/41#issuecomment-1660680471  

Thank you very much @pdimov! As a followup question, `Boost::numeric_ublas` lib is needed in my project. However i see that `boost/libs/numeric/ublas` submodule does not have a CMakeLists.txt. This causes the line in `boost/tools/cmake/include/BoostRoot.cmake` [here](https://github.com/boostorg/cmake/blob/69f16e2819c4686450bdac807e3eb58346fb2ac2/include/BoostRoot.cmake#L215) to not recognize `numeric/ublas` as a target.   
  
Do you happen to know what's special about it that it lacks a top-level CMakeLists.txt, and what should one do to make `Boost::numeric_ublas` a target so that I can add it using the method you demonstrate above?   
  
As a reference, `numeric/conversion` and `numeric/interval`  are both fine, since they have their top-level CMakeLists.txt. Thanks a lot! (I could copy the other `numeric/*` lib's CMakeLists.txt, just wondering why ublas is different here!)

---

## Comment 5

> Username: pdimov  
> Created at: 2023-08-01 17:26:37 UTC  
> Url: https://github.com/boostorg/cmake/issues/41#issuecomment-1660782408  

I submitted a pull request (https://github.com/boostorg/ublas/pull/142) to add a CMakeLists.txt file to `ublas`, but the maintainers haven't merged it yet. I'll ask them again in the PR.

---

## Comment 6

> Username: q-ycong-p  
> Created at: 2023-08-01 20:51:19 UTC  
> Updated at: 2023-08-01 21:06:59 UTC  
> Url: https://github.com/boostorg/cmake/issues/41#issuecomment-1661074719  

TLDR: Is there a way where a project uses header-only components of the Boost libraries, and avoid building them, with the above `Boost::boost` approach?  
  
---  
  
Hi @pdimov , sorry that I am extending on this thread. Pls let me know if I'd better take these how-to questions to another thread, and close this issue (since it is not a boostorg/cmake bug!).  
  
First, thanks for pointing me to the ublas PR. I patched with the PR change and unblocked.  
  
However, my project should only need header-only Boost. Due to its cross-compilation nature, I'd like to avoid building the Boost CXX objects since it suffices as long as headers are included. However, with [the above approach](https://github.com/boostorg/cmake/issues/41#issuecomment-1659840318) through `Boost::boost`, linking happens error-free, but building of the project triggers `.cpp.obj` being build for a number of Boost libraries.  
  
Below is how I'm using Boost in my project. Does it look like I am doing something obviously off? Ty!!  
```  
# Project top-level macro  
set(BOOST_INCLUDE_LIBRARIES core system variant numeric/ublas)  
add_subdirectory(${NEMORTLIB_BOOST_SOURCE_DIR} ${NEMORTLIB_BOOST_BUILD_DIR} EXCLUDE_FROM_ALL)  
  
if(NOT TARGET Boost::boost)  
    add_library(Boost::boost INTERFACE IMPORTED)  
    set_property(TARGET Boost::boost APPEND PROPERTY  
                    INTERFACE_LINK_LIBRARIES Boost::core  
                                            Boost::system  
                                            Boost::variant  
                                            Boost::numeric_ublas)  
endif()  
  
#  Subdirectory A  
target_link_libraries(target_a PRIVATE target_b Boost::boost)  
```  
  
My suspicion is that, a bunch of transitive boost deps are being built due to `ublas` (e.g. file_system). However my project should only needs the header-only component of `ublas`. Will it make sense if my project manually moves all boost headers into one directory, which mimics the state after a Boost installation? If so, is there a place in `boostorg/cmake` that's already doing it?

---

## Comment 7

> Username: pdimov  
> Created at: 2023-08-01 21:20:50 UTC  
> Url: https://github.com/boostorg/cmake/issues/41#issuecomment-1661116850  

Your guess seems correct; `ublas` links both directly to a library that needs building (`serialization`) and indirectly via `compute` to e.g. `filesystem` (see the [dependency report](https://pdimov.github.io/boostdep-report/master/numeric~ublas.html)).  
  
I'm not sure how this can be fixed; in CMake, if you link to a library, you link to its transitive dependencies, which means they will have to be built. You can in principle patch the CMakeLists.txt file for `ublas` and remove the unneeded dependencies, if that works in your case.  
  
As for copying the headers somewhere, that's what the installation step does. I don't think it's possible to achieve this effect with `add_subdirectory`.

---

## Comment 8

> Username: q-ycong-p  
> Created at: 2023-08-02 00:17:42 UTC  
> Url: https://github.com/boostorg/cmake/issues/41#issuecomment-1661275235  

Thank you very much @pdimov! That makes a lot of sense to me.  
  
Given what you said, I experimented with using `target_include_directories` instead of `target_link_libraries` to avoid building things transitively. What I struggle is that it now seems to require manually adding all direct AND indirect boost libraries to `BOOST_INCLUDE_LIBRARIES`:  
```  
set(BOOST_INCLUDE_LIBRARIES ...) # Here, each transitive dependency needs to be identified and added  
add_subdirectory(${BOOST_SOURCE_DIR} ${BOOST_BUILD_DIR} EXCLUDE_FROM_ALL) # Here, it starts building CXX objects for unneeded boost libs if without EXCLUDE_FROM_ALL  
```  
  
This seems to imply that deps transitivity is resolved with `target_link_libraries` but not resolved with `target_include_directories`.  Does it look like I am missing a easy solution?

---

## Comment 9

> Username: pdimov  
> Created at: 2023-08-02 03:27:01 UTC  
> Url: https://github.com/boostorg/cmake/issues/41#issuecomment-1661434049  

That is how it works, yes.  
  
I still don't see a better way to accomplish what you want than removing `Boost::compute` and `Boost::serialization` from the `ublas` dependencies; either by patching the CMakeLists.txt file - which you can do because you're adding it anyway - or as a "post-processing step" by getting the INTERFACE_LINK_LIBRARIES property of the `Boost::numeric_ublas` target, using `list(REMOVE_ITEM` to remove those two, and setting it back.

---

## Comment 10

> Username: q-ycong-p  
> Created at: 2023-08-02 18:46:20 UTC  
> Updated at: 2023-08-02 18:46:59 UTC  
> Url: https://github.com/boostorg/cmake/issues/41#issuecomment-1662773274  

Thank you @pdimov!! Removing these two deps uncovers issue of my own understanding - even though my main project depends on header-only boost, my test has indirect dependency on `Boost::serialization` via ublas's vector and matrix - now I've disabled those tests for my cross-compilation case, and I am able to get through my cmake build issues related to Boost. Thanks again for your prompt and accurate help!

---

## Comment 11

> Username: q-ycong-p  
> Created at: 2023-08-02 19:04:31 UTC  
> Url: https://github.com/boostorg/cmake/issues/41#issuecomment-1662812948  

Closing the issue since @pdimov has explained this to be the intended behavior with add_subdirectory usage, and suggested working solutions. Ty!
