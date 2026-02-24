# #490 Add a Boost-friendly subproject case to CMakeLists [Merged]

> Username: pdimov  
> Created at: 2021-06-04 17:59:50 UTC  
> Updated at: 2021-10-29 17:20:47 UTC  
> Merged at: 2021-07-26 21:45:51 UTC  
> Closed at: 2021-07-26 21:45:51 UTC  
> Url: https://github.com/boostorg/hana/pull/490  



---

## Review 1 [Commented]

> Username: ldionne  
> Created_at: 2021-06-04 18:01:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/490#pullrequestreview-676543610  

📁 CMakeLists.txt

```diff
  22 |+     Boost::mpl
  23 |+     Boost::tuple
  24 |+ )
```

> Username: ldionne  
> Created_at: 2021-06-04 18:01:47 UTC  
> Url: https://github.com/boostorg/hana/pull/490#discussion_r645757103  

So those are made available by means of a superproject including this file and defining those targets?

> Username: pdimov  
> Created_at: 2021-06-04 18:43:21 UTC  
> Url: https://github.com/boostorg/hana/pull/490#discussion_r645779915  

Usually, yes. Another supported workflow (or at least, a workflow I would like Boost to support) is the user manually including the required Boost dependencies with `add_subdirectory`, whether as submodules or with `FetchContent`.  
  
All Boost libraries are expected to define a `Boost::libname` target.  
  
(This should also work with a CMake-installed Boost - the installed config file for a library defines the `Boost::libname` imported target.)


---

## Comment 2

> Username: ldionne  
> Created_at: 2021-06-04 18:02:39 UTC  
> Url: https://github.com/boostorg/hana/pull/490#issuecomment-854908855  

Thanks for the PR! I'd like to understand what would be required to make the main CMake file compatible with the rest of Boost instead - I think it would be easier to maintain in the future, and I'd like Hana to be a good citizen of Boost as much as possible.

---

## Comment 3

> Username: pdimov  
> Created_at: 2021-06-04 18:58:39 UTC  
> Url: https://github.com/boostorg/hana/pull/490#issuecomment-854938854  

The current `CMakeLists` only seems suited for root project use, so getting from here to there is not a small diff. Clean subprojects are easier to make Boost-compatible, although there are still many things that will be "wrong".  
  
The project name must be `boost_libname` by convention; the version when inside the Boost superproject must be set to `BOOST_SUPERPROJECT_VERSION` (e.g. "1.77.0".) There must be a clean target `boost_libname` with an alias `Boost::libname` that other libraries (Boost and not) can link to. `target_include_directories` must be `include` to enable the superproject automatic install feature; or if not, the library can supply its own installation, but the package name must be `boost_libname`, the package version must match the Boost version, and the config file must declare the `Boost::libname` target and issue the required `find_dependency` calls that match `target_link_libraries`. (The destinations should also be "correct", i.e. those given by `GNUInstallDirs` to which CMake 3.14 defaults.)  
  
As few options as possible must be declared, preferably none unless they really control how the library is built; as few lines of status output must be produced, preferably none. (For 140+ libraries, if each decides to add four options and print seven lines of output, the result is not hard to imagine.) Adding any compile flags is also not a good practice; these should be left to the master project. And of course `find_package(Boost)` is just asking for trouble. :-)  
  
I'm working on a document to spell all this out, but it's not done yet.

---

## Comment 4

> Username: ldionne  
> Created_at: 2021-07-26 21:45:37 UTC  
> Url: https://github.com/boostorg/hana/pull/490#issuecomment-887048832  

Thanks for the explanation and sorry for sitting on this. Let's land this, and in the future, I can work on updating the main CMakeLists.txt to be Boost-compatible if I have time. Otherwise, I don't want to stall your effort on that.

---

## Comment 5

> Username: pdimov  
> Created_at: 2021-09-16 11:42:01 UTC  
> Url: https://github.com/boostorg/hana/pull/490#issuecomment-920828379  

Thanks for merging that; please let me know when it's in master, so that I can remove `hana` from `BOOST_INCOMPATIBLE_LIBRARIES`.

---

## Comment 6

> Username: pdimov  
> Created_at: 2021-10-28 19:05:41 UTC  
> Url: https://github.com/boostorg/hana/pull/490#issuecomment-954121563  

Would you please merge this to master before 1.78.0?

---

## Comment 7

> Username: ldionne  
> Created_at: 2021-10-29 17:20:47 UTC  
> Url: https://github.com/boostorg/hana/pull/490#issuecomment-954913255  

Done, sorry it took so long, I'm somewhat overloaded with libc++.

---
