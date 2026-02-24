# #27 Create proper CMake files [Merged]

> Username: Flamefire  
> Created at: 2017-10-01 22:48:20 UTC  
> Updated at: 2019-12-27 17:06:03 UTC  
> Merged at: 2019-12-27 17:05:59 UTC  
> Closed at: 2019-12-27 17:05:59 UTC  
> Url: https://github.com/boostorg/nowide/pull/27  

This adds proper CMakefiles for building the library or consumptions as a sub-project. Hence it is possible to add the `src` with `add_subdirectory` to an existing build.  
  
To build everything with tests, use the central CMakeLists.txt.  
  
The CMakeLists auto-detects the standalone version.   
  
Fixes #20

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2019-12-18 16:32:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/nowide/pull/27#pullrequestreview-334102340  

📁 CMakeLists.txt

```diff
  75 |+     set(Boost_USE_STATIC_LIBS ON)
  76 |+   endif()
  77 |+   find_package(Boost 1.55 REQUIRED)
```

> Username: pdimov  
> Created_at: 2019-12-18 16:31:59 UTC  
> Updated_at: 2019-12-27 13:26:35 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#discussion_r359444984  

When you're built as part of the Boost superproject (https://github.com/boostorg/boost/blob/develop/CMakeLists.txt) you shouldn't issue `find_package(Boost)`.  
  
My current "model" library is https://github.com/boostorg/timer/blob/develop/CMakeLists.txt, https://github.com/boostorg/timer/blob/develop/.travis.yml#L149-L154.

> Username: Flamefire  
> Created_at: 2019-12-18 16:42:50 UTC  
> Updated_at: 2019-12-27 13:26:35 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#discussion_r359451017  

Oh, great!  
  
Ok so I guess I can simply put that inside an `if` for `BOOST_ENABLE_CMAKE`? I definitely need it to work with a (b2) installed boost for development and consumption as git submodule, that's why the find_package.  
  
If you see anything else, feel free to comment.  
  
A tip in return: You can simplify https://github.com/boostorg/timer/blob/0ed7b642a9a6ddddc7d838a2edf6d9a70692a16e/CMakeLists.txt#L25-L30 to something like https://github.com/boostorg/nowide/pull/27/files/b7141f8757d515f187cb9fd65a46bb2b4a94aca8#diff-af3b638bc2a3e6c650974192a53c7291R61-R65. CMake already has some logic for shared libs definitions. There won't be a static definition but e.g. `BOOST_TIMER_STATIC_LINK` is never used (or is it?). IMO the `*_DYNAMIC_LINK` is enough anyway.

> Username: pdimov  
> Created_at: 2019-12-18 17:02:42 UTC  
> Updated_at: 2019-12-27 13:26:35 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#discussion_r359461620  

The correct check would be for either BOOST_SUPERPROJECT_VERSION or BOOST_SUPERPROJECT_SOURCE_DIR being defined. BOOST_ENABLE_CMAKE is temporary; at some point when we officially support building with CMake we'll remove it.  
  
Defining options when inside the superproject will probably be declared bad practice at some point (because if 140 libraries define 4 options each, the user will have a lot to scroll through), but this may be a lost battle.  
  
My current thinking is that we should install the config files to `cmake/{PROJECT_NAME}-{PROJECT_VERSION}`, same as the current `b2 install`. But I'm not sure what libraries that use their own versioning ought to do. I certainly intend to require exact version match in all my `find_dependency` calls, but if nobody depends on Nowide, this won't matter for you.  
  
We'll figure these things out with time, I guess. For now, it's important for libraries to be good citizens and not break the superproject, we'll see about the rest.  
  
I didn't know about DEFINE_SYMBOL, thanks.

> Username: Flamefire  
> Created_at: 2019-12-18 17:20:16 UTC  
> Updated_at: 2019-12-27 13:26:35 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#discussion_r359470586  

> My current thinking is that we should install the config files to cmake/{PROJECT_NAME}-{PROJECT_VERSION}, same as the current b2 install.  
  
Does it? How would a find_package be able to find that? When installing to `cmake/PROJECT_NAME` it will be found by `find_package(PROJECT_NAME)`. Or is there something that extends the search-prefixes?  
  
> But I'm not sure what libraries that use their own versioning ought to do  
  
It seems for CMake this does not matter unless we to the same name mangling that b2 does: You won't be able to install a library with 2 different versions into the same prefix. And per prefix the search is perfectly fine. When you provide a "VersionConfig", you can even have all those prefixes in your PREFIX_PATH and the search will pick the first matching version while discarding the rest. So I think the way to go there would be to install into e.g. `/opt/boost_<VERSION` and users add each of them to their CMAKE_PREFIX_PATH.  
  
But well, this gonna take some time.  
  
> Defining options when inside the superproject will probably be declared bad practice at some point  
  
I tend to disagree. When those options have reasonable defaults, then why not? They should be "standardized" though. That's how I chose mine. Because if you have to edit a CMakeLists or so to not build the tests or not install... that would be very inconvenient. And having an option to disable Werror is also a good idea (I'm pro leaving that on and building with highest warning settings, catches errors early and leaves less log clutter for users)

> Username: pdimov  
> Created_at: 2019-12-18 17:34:04 UTC  
> Updated_at: 2019-12-27 13:26:35 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#discussion_r359476938  

> How would a find_package be able to find that?  
  
It does. Try it.  
  
> When those options have reasonable defaults, then why not?  
  
Well the obvious reason is that 4 * 140 = 560. Even if every option by itself makes perfect sense, should a user really be expected to wade through 560 options? Doesn't seem very practical, especially when most of these will overlap so you'll want to set, say, 80 of them at once because they do the same thing.

> Username: Flamefire  
> Created_at: 2019-12-19 09:09:12 UTC  
> Updated_at: 2019-12-27 13:26:35 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#discussion_r359752825  

Ok nice, how did you know? It is not mentioned in the docu... Well, if using that then we'd need to suffix the version to the libraries or the configs will pick the last installed version  
  
That's why I said a standardized set and useful defaults. I imagine 2 use cases:  
- installing all of boost, either with all tests or without all tests  
- testing a single library -> disable all tests + installation, enable for the single library  
  
Hence the default value for the boost super build powered run should be the same standardized variables with simple Boost_ prefix: `option(Boost_NOWIDE_INSTALL "Install library" ${Boost_INSTALL})`  
  
Btw: In https://github.com/boostorg/boost/blob/develop/CMakeLists.txt we should use a "good" project name to avoid setting variables with different prefixes. If "Boost" is already taken by the b2 build, then BoostSuper? BoostRoot? BOOST_SUPERPROJECT? (But that is to screamy). Or just keep it with "Boost" and one can check for "Boost_SOURCE_DIR" to see if build as part of it.

> Username: Flamefire  
> Created_at: 2019-12-19 12:45:25 UTC  
> Updated_at: 2019-12-27 13:26:35 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#discussion_r359841678  

Have you seen https://gitlab.otris.de/vendor/boost_cmake? Might be good for inspiration but it doesn't seem to be that good...


---

## Comment 2

> Username: Flamefire  
> Created_at: 2019-12-20 13:17:16 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-567921181  

@pdimov I think I'm done. I created special CMake functions for adding and installing a library which are reusable and create versioned libraries if requested.

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-12-20 17:31:55 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568011102  

I don't think you should be calling your cmake scripts BoostThis (and functions boost_this). These names are reserved for tools/cmake/include. When I add BoostInstall.cmake there, for instance, you will pick that up instead of your own (because you add your cmake/ directory to the end of the search path).  
  
By convention tests should be prefixed with {PROJECT_NAME} (or contain it somewhere in the name) both to enable `ctest -R BoostNowide` and to avoid conflicts in .exe names such as "test_env", which may well be used by some other library. The superproject builds everything into a single bin/ directory to enable Windows tests to work because there's no RPATH on Windows.  
  
You don't seem to test the superproject configuration.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2019-12-20 18:01:35 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568021880  

> I don't think you should be calling your cmake scripts BoostThis (and functions boost_this). These names are reserved for tools/cmake/include. When I add BoostInstall.cmake there, for instance, you will pick that up instead of your own (because you add your cmake/ directory to the end of the search path).  
  
Actually this is intentional. Those are supposed to eventually go into boost/cmake. I used the parser syntax to allow backward compatible changes. So this is a kind of test for them. Or shall I rename them anyway?  
  
> By convention tests should be prefixed with {PROJECT_NAME}  
  
Good point! :+1:  
  
> You don't seem to test the superproject configuration.  
  
How would I do that? The superproject suffers IMO from a big shortcoming: You can't build a single library and its test without building everything, can you?

---

## Comment 5

> Username: pdimov  
> Created_at: 2019-12-20 18:14:50 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568029472  

I test with `ctest -R boost_timer`, as I already said a few times. See https://github.com/boostorg/timer/blob/develop/.travis.yml#L149-L154 and https://github.com/boostorg/timer/blob/develop/appveyor.yml#L78-L92.  
  
My tests don't build on `cmake --build`, they build on `ctest`, but either way should work.

---

## Comment 6

> Username: Flamefire  
> Created_at: 2019-12-20 19:29:11 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568058387  

Something similar can be achieved by having `<LIB>_BUILD_TESTS` default to `BOOST_BUILD_TESTS`, then configure with `-DBOOST_BUILD_TESTS=OFF -DNOWIDE_BUILD_TESTS=ON` which might be better although both have benefits. Pretty neat solution indeed.  
  
But my point was: Imagine all 141 libraries have a CMakeLists and you want to build only your tests, then `cmake --build .` will all libraries first.  
  
Idea: Use your solution, use `add_subdirectory(... EXCLUDE_FROM_ALL)` in https://github.com/boostorg/cmake/blob/develop/include/BoostRoot.cmake and omit the `cmake --build` in the CI. Building a test will build all dependencies. Oh, but running tests in parallel will cause a race in building those.     
Better: Change https://github.com/boostorg/cmake/blob/develop/include/BoostRoot.cmake to always add all libraries (with CMakeLists) and use `BOOST_INCLUDE_LIBRARIES` to determine on which to (not) set EXCLUDE_FROM_ALL. This way `make all` will build the selected libraries and its dependencies only and the test-build trickery is not required. This will probably also run faster as the build system (make, ninja, MSVC) has all the info it need to build everything in parallel.

---

## Comment 7

> Username: pdimov  
> Created_at: 2019-12-20 19:36:00 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568060392  

I don't want to use EXCLUDE_FROM_ALL; building Boost with CMake should build all libraries, exactly as building it with b2 does today.

---

## Comment 8

> Username: Flamefire  
> Created_at: 2019-12-20 19:39:43 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568061482  

Sure. If `BOOST_INCLUDE_LIBRARIES` is not set (default) then no `EXCLUDE_FROM_ALL` is added. If it is set, `EXCLUDE_FROM_ALL` is added to all but the ones in that list. This is exactly as `./b2` and `./b2 --with-libraries=...` With the current approach a user would need to know all dependencies of a library to use `BOOST_INCLUDE_LIBRARIES` because e.g. if I do `-DBOOST_INCLUDE_LIBRARIES=nowide` then it will fail because the target `Boost::config` is not defined. Although you'd need a "blacklist" of libs with broken/non-compliant CMakeLists

---

## Comment 9

> Username: pdimov  
> Created_at: 2019-12-20 19:43:45 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568062685  

Ah right, this makes sense.  
  
I already have the blacklist.

---

## Comment 10

> Username: Flamefire  
> Created_at: 2019-12-20 19:45:57 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568063322  

This one? https://github.com/boostorg/cmake/blob/ed7ff3e547f7a8b7c4d7d7a77376d93f4e48171b/include/BoostRoot.cmake#L15  
  
I meant (CMake vs b2):  
- `BOOST_INCLUDE_LIBRARIES` == `--with-libraries`  
- `BOOST_EXCLUDE_LIBRARIES ` == `--without-libraries`  
- `BOOST_NON_CMAKE_LIBRARIES` (no equivalent in b2 as not required there)

---

## Comment 11

> Username: pdimov  
> Created_at: 2019-12-20 20:09:50 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568071701  

This works as expected for building the library and its dependencies, but unfortunately, `ctest` still sees the tests of the libraries added with EXCLUDE_FROM_ALL. Could be a CMake issue.

---

## Comment 12

> Username: pdimov  
> Created_at: 2019-12-20 20:11:25 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568072309  

I use BOOST_EXCLUDE_LIBRARIES as the blacklist at the moment. We'll see if there's a need to change it.

---

## Comment 13

> Username: Flamefire  
> Created_at: 2019-12-20 20:20:26 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568074856  

> This works as expected for building the library and its dependencies, but unfortunately, ctest still sees the tests of the libraries added with EXCLUDE_FROM_ALL  
  
It's a design issue. What do we want? Being able to explicitly build the library (`make <lib>`) and their tests (`ctest -R <lib>`)? Or remove the tests completely if the library was not requested? `EXCLUDE_FROM_ALL` does not affect CTest at all, only the make process. I'd suggest to go with the `BOOST_<lib>_BUILD_TESTS` option and disable that when setting `EXCLUDE_FROM_ALL`  
  
> I use BOOST_EXCLUDE_LIBRARIES as the blacklist at the moment. We'll see if there's a need to change it.  
  
I'd have one: `./b2 --without-python`. I often had to build all but 1 or 2 due to the environment used, python was most common because there were $ISSUES. So IMO we need separate exclude and black lists. It's easily implemented.

---

## Comment 14

> Username: Flamefire  
> Created_at: 2019-12-22 15:41:25 UTC  
> Updated_at: 2019-12-22 16:12:53 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568273574  

I can't build with the super project build because BoostLocale has no support for CMake. This also shows a problem that this library will have too: How to handle libraries that have header-only parts? E.g. BoostLocale has the UTF conversion functions which are explicitly header-only. However I can only link to `boost::locale` which compiles and links the full library before removing almost everything again (if it had a CMakeLists). Same here: Basically everything but iostreams is header-only. Previously I had `nowide::headers`, `nowide::static`, `nowide::shared`, but now the prefix is already taken. Shall we assume there is always a boost super-project and we have `boost::headers`? Will there be the headers of BoostLocale when it isn't explicitly installed? Do we care? Or enforce it via meta-data (similar to the getdeps tool)?  
  
I could also pull out the required headers (only 2-3 I think) of BoostLocale (done for the standalone already) to avoid that dependency.     
Edit: Actually it is just a single header: `utf.hpp`. I'd just copy it to this repo. What do you say?

---

## Comment 15

> Username: Flamefire  
> Created_at: 2019-12-24 17:50:48 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568784718  

@pdimov If there are no objections I'll merge this once CI passes. We can improve on the CMake scripts in follow-up PRs where required. It should be consistent with other Boost CMakeLists and install layout now with default params given.

---

## Comment 16

> Username: pdimov  
> Created_at: 2019-12-24 22:51:28 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568808748  

Copying the header somewhere in detail/ sounds reasonable.

---

## Comment 17

> Username: pdimov  
> Created_at: 2019-12-24 22:55:28 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568808961  

I don't think I have any objections. The important thing for me is to not break the superproject and to not add any tests when BUILD_TESTING is OFF, and from a quick look both are met as you don't enable the tests at all (unless BOOST_BUILD_TESTS or BOOST_NOWIDE_BUILD_TESTS are set, which they aren't).

---

## Comment 18

> Username: Flamefire  
> Created_at: 2019-12-25 15:09:10 UTC  
> Url: https://github.com/boostorg/nowide/pull/27#issuecomment-568907999  

Good catch, I meant to change that too. I removed the `*_BUILD_TESTS` options in favour of `BUILD_TESTING ` and set the defaults for the other options to the same as if directly building BoostNowide (so e.g. installation enabled)  
  
The superproject integration is also tested but installation is disabled for now. Will double-check this tomorrow to see if I missed anything and then finally merge

---
