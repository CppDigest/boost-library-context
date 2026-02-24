# #94 Add BOOST_DLL_USE_STD_FS option [Merged]

> Username: yurybura  
> Created at: 2025-04-11 09:02:36 UTC  
> Updated at: 2025-08-17 09:52:08 UTC  
> Merged at: 2025-06-10 15:47:05 UTC  
> Closed at: 2025-06-10 15:47:05 UTC  
> Url: https://github.com/boostorg/dll/pull/94  

Makes Boost.Filesystem optional in CMake.

---

## Comment 1

> Username: Osyotr  
> Created_at: 2025-04-11 11:17:23 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-2796613018  

IMHO the dependency should just be removed. To use or not to use `Boost::filesystem` cannot be decided on `Boost::dll` installation step.

---

## Comment 2

> Username: yurybura  
> Created_at: 2025-04-11 11:21:32 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-2796621175  

> IMHO the dependency should just be removed. To use or not to use `Boost::filesystem` cannot be decided on `Boost::dll` installation step.  
  
For correct generation of CMake configs it is better to have an option.

---

## Comment 3

> Username: Osyotr  
> Created_at: 2025-04-11 11:25:47 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-2796629264  

> For correct generation of CMake configs it is better to have an option.  
  
My point is that such configs are incorrect. They force `Boost::filesystem` even if I don't want it or they don't force it when I do want it.

---

## Comment 4

> Username: apolukhin  
> Created_at: 2025-06-10 15:47:35 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-2959783296  

Many thanks for the PR!

---

## Comment 5

> Username: Osyotr  
> Created_at: 2025-06-10 16:04:36 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-2959849664  

@apolukhin since you've merged this PR as is please clarify how to use Boost::dll in both modes when Boost is not part of the project (i.e. it's coming from vcpkg/conan/apt/rpm).

---

## Comment 6

> Username: apolukhin  
> Created_at: 2025-06-11 16:20:08 UTC  
> Updated_at: 2025-06-11 16:20:45 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-2963447295  

> @apolukhin since you've merged this PR as is please clarify how to use Boost::dll in both modes when Boost is not part of the project (i.e. it's coming from vcpkg/conan/apt/rpm).  
  
IIRC at least Conan provides a way to specify CMake build options for a dependency ()boost::dll).  
  
So the packaging tool should provide such customization, or package tool users should request such functionality from the tool vendors.

---

## Comment 7

> Username: Osyotr  
> Created_at: 2025-06-11 16:37:48 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-2963495801  

> So the packaging tool should provide such customization, or package tool users should request such functionality from the tool vendors.  
  
Of course you can force package managers to build boost with certain flags/options. But the problem is that whether to use boost-filesystem is not an option. It's an alternative.  
You can't have one thing that uses boost-dll with boost-filesystem and another thing that uses boost-dll with std::filesystem. You would have to have two separate boost installations for this.  
This is why I recommended to just remove boost-filesystem dependency and let users decide how they consume boost-dll on a per-project (per-target) manner. For example:  
- If you want to use boost-dll with boost-filesystem, just pass your `boost::filesystem::path`s to boost-dll API. You already link with boost-filesystem, otherwise you wouldn't be able to create `boost::filesystem::path` in the first place.  
- If you want to use boost-dll with std::filesystem, just define `BOOST_DLL_USE_STD_FS` in your project/target to switch API to accept `std::filesystem::path`s.

---

## Comment 8

> Username: apolukhin  
> Created_at: 2025-06-11 16:57:28 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-2963546629  

> * If you want to use boost-dll with boost-filesystem, just pass your `boost::filesystem::path`s to boost-dll API. You already link with boost-filesystem  
  
That's not actually required. `Boost::filesystem` is the `INTERFACE` dependency, so it implicitly adds `Boost::filesystem` to every user of `Boost::dll`. In other words, project with the following in CMakeLists.txt:  
```  
target_link_libraries(${PROJECT_NAME} PUBLIC Boost::dll)  
```  
explicitly gets the `Boost::filesystem`.  
  
Customization through linked into targets seems to be much more confusing than customization through explicit options.

---

## Comment 9

> Username: Osyotr  
> Created_at: 2025-06-11 17:36:08 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-2963649662  

> Customization through linked into targets seems to be much more confusing than customization through explicit options.  
  
Explicitly linking `Boost::filesystem` in a project that uses it is certainly not a bad thing to do.  
With my proposal it's perfectly fine to use boost-dll with std::filesystem **and** link to `Boost::filesystem`.  
The real customization point should be `BOOST_DLL_USE_STD_FS` preprocessor definition.

---

## Comment 10

> Username: apolukhin  
> Created_at: 2025-06-11 17:51:35 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-2963689826  

> With my proposal it's perfectly fine to use boost-dll with std::filesystem **and** link to `Boost::filesystem`.  
  
With current approach you get the same result by defining the `-DBOOST_DLL_USE_STD_FS=ON` CMake option and linking the project with `Boost::filesystem`. The `BOOST_DLL_USE_STD_FS` preprocessor definition is still the customization point, just slightly less boilerplate in defining it in CMake

---

## Comment 11

> Username: Osyotr  
> Created_at: 2025-06-11 18:13:10 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-2963748673  

> With current approach you get the same result by defining the `-DBOOST_DLL_USE_STD_FS=ON` CMake option and linking the project with `Boost::filesystem`.   
  
`BOOST_DLL_USE_STD_FS`, the CMake option, makes no sense when I build *my* project. The installed CMake config for `Boost::dll` has burned-in `INTERFACE_LINK_LIBRARY Boost::filesystem` or `INTERFACE_COMPILE_DEFINITIONS BOOST_DLL_USE_STD_FS` depending on what what value for `BOOST_DLL_USE_STD_FS` was used when `Boost::dll` was being installed.

---

## Comment 12

> Username: apolukhin  
> Created_at: 2025-06-14 07:56:07 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-2972455481  

How about having two libraries Boost::dll_boost_fs and Boost::dll_st_fs, Boost::dll being an alias to Boost::dll_boost_fs (for now)?

---

## Comment 13

> Username: apolukhin  
> Created_at: 2025-07-08 13:06:03 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-3048894940  

The install script gone nuts with this change. Made separate targets in https://github.com/boostorg/dll/commit/4105c3a8f8d5287b59b0185c45d4c9a768d92a55 to workaround the issue

---

## Comment 14

> Username: Osyotr  
> Created_at: 2025-07-08 15:57:13 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-3049493360  

> How about having two libraries Boost::dll_boost_fs and Boost::dll_st_fs, Boost::dll being an alias to Boost::dll_boost_fs (for now)?  
  
It doesn't change the fact that I need to build `Boost::filesystem` just to be able to use `Boost::dll`.  
And it also doesn't scale well (what about `BOOST_DLL_USE_BOOST_SHARED_PTR`?).

---

## Comment 15

> Username: pdimov  
> Created_at: 2025-08-17 09:15:48 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-3194251314  

Since DLL includes Boost.Filesystem headers (I assume), Boost::dll has to link to Boost::filesystem, or the include will fail.  
  
There's no elegant way in CMake to say "this library only depends on that target's include path, but does not link to the compiled lib." (And even if there were, it would probably create more problems than it solves.)

---

## Comment 16

> Username: Osyotr  
> Created_at: 2025-08-17 09:52:08 UTC  
> Url: https://github.com/boostorg/dll/pull/94#issuecomment-3194275093  

> Since DLL includes Boost.Filesystem headers (I assume), Boost::dll has to link to Boost::filesystem, or the include will fail.  
  
Only if `BOOST_DLL_USE_STD_FS`, the preprocessor definition, is defined.

---
