# #90 Avoid installing libraries multiple times or issueing wrong message [Merged]

> Username: Flamefire  
> Created at: 2025-09-28 10:51:54 UTC  
> Updated at: 2025-10-01 14:55:54 UTC  
> Merged at: 2025-09-28 18:58:52 UTC  
> Closed at: 2025-09-28 18:58:52 UTC  
> Url: https://github.com/boostorg/cmake/pull/90  

Some libraries call `boost_install` themselves which adds `INSTALL_INTERFACE` to their `INTERFACE_INCLUDE_DIRETORIES` property. That then makes the install check in `__boost_auto_install` fail and print a message that the library won't be installed while it will be.  
  
Example:  
> -- Not enabling installation for 'serialization'; interface include directory '$<BUILD_INTERFACE:C:/git/boost/libs/serialization/include>;$<INSTALL_INTERFACE:include/boost-1_87>' does not equal 'C:/git/boost/libs/serialization/include' or '$<BUILD_INTERFACE:C:/git/boost/libs/serialization/include>'  
  
Use a label to detect targets for which `boost_install` was already called and skip the logic in `__boost_auto_install` for those.  
  
Libs possibly affected:  
```  
libs/asio/CMakeLists.txt:  boost_install(  
libs/dll/CMakeLists.txt:  boost_install(  
libs/fiber/CMakeLists.txt:  boost_install(TARGETS boost_fiber boost_fiber_numa VERSION ${BOOST_SUPERPROJECT_VERSION} HEADER_DIRECTORY include)  
libs/log/CMakeLists.txt:    boost_install(TARGETS ${boost_log_install_targets} VERSION "${BOOST_SUPERPROJECT_VERSION}" HEADER_DIRECTORY include)  
libs/mp11/CMakeLists.txt:  boost_install(TARGETS boost_mp11 HEADER_DIRECTORY include/)  
libs/python/CMakeLists.txt:  boost_install(TARGETS ${_boost_python} boost_python VERSION ${BOOST_SUPERPROJECT_VERSION} HEADER_DIRECTORY include)  
libs/python/CMakeLists.txt:  boost_install(TARGETS ${_boost_numpy} boost_numpy VERSION ${BOOST_SUPERPROJECT_VERSION})  
libs/serialization/CMakeLists.txt:  boost_install(TARGETS boost_serialization boost_wserialization VERSION ${BOOST_SUPERPROJECT_VERSION} HEADER_DIRECTORY include)  
libs/stacktrace/CMakeLists.txt:    boost_install(TARGETS boost_stacktrace_${suffix} VERSION ${BOOST_SUPERPROJECT_VERSION} HEADER_DIRECTORY include)  
libs/test/CMakeLists.txt:  boost_install(  
```

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-09-28 13:32:11 UTC  
> Url: https://github.com/boostorg/cmake/pull/90#issuecomment-3343383657  

I don't think the LABELS property is the one to use. It means something different entirely.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-09-28 13:37:44 UTC  
> Url: https://github.com/boostorg/cmake/pull/90#issuecomment-3343401171  

To me it sounds like they are arbitrary tags: https://cmake.org/cmake/help/v3.13/prop_tgt/LABELS.html  
  
> Target label semantics are currently unspecified.  
  
Using a Boost prefix to avoid them clashing with anything in the future

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-09-28 13:48:36 UTC  
> Url: https://github.com/boostorg/cmake/pull/90#issuecomment-3343434107  

This property holds the test labels. It also doesn't work at least on CMake 3.16 (LABELS is not a whiteisted property.)

---

## Comment 4

> Username: Flamefire  
> Created_at: 2025-09-28 16:50:40 UTC  
> Url: https://github.com/boostorg/cmake/pull/90#issuecomment-3343872978  

According to [the docs](https://cmake.org/cmake/help/v3.13/search.html?q=labels) you can have labels for targets, source files, directories and tests. That's 2 different results in the end: Labels on tests and labels on targets with the 2nd not having any meaning.  
  
But seems like they didn't add that to the whitelist for INTERFACE targets.  
  
I found [this](https://cmake.org/cmake/help/latest/release/3.11.html#id15) in CMake 3.11:  
> [Interface Libraries](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#interface-libraries) may now have custom properties set on them if they start with either an underscore (_) or a lowercase ASCII character. The original intention was to only allow properties which made sense for INTERFACE libraries, but it also blocked usage of custom properties.  
  
  
That should be OK for our use case. If the property isn't set it will be false-ish so the check is easy

---

## Comment 5

> Username: pdimov  
> Created_at: 2025-09-28 17:42:22 UTC  
> Url: https://github.com/boostorg/cmake/pull/90#issuecomment-3343966136  

I was going to suggest just checking the INTERFACE_INCLUDE_DIRECTORIES property for the value placed by `boost_install`, but I suppose the custom property works as well.

---

## Comment 6

> Username: Flamefire  
> Created_at: 2025-09-28 18:20:26 UTC  
> Url: https://github.com/boostorg/cmake/pull/90#issuecomment-3344070496  

That wasn't safe enough for me because an individual library could set those pretty conventional paths already, e.g. if it has a standalone version. Unfortunately there isn't any check if a specific export set exists or if a target is part of one, which would be a direct way to check for installation  
  
Anyway, CI looks good.     
Maybe it's worth adding a couple configure-only checks using different CMake versions. Could be a single job looping over some key versions (3.5, 3.6, 3.8, 3.16, 3.19, last 3.x, 4.0, 4.1 come to mind) as they seem to be available as binary packages which should make this fast enough

---

## Comment 7

> Username: pdimov  
> Created_at: 2025-09-28 18:59:34 UTC  
> Url: https://github.com/boostorg/cmake/pull/90#issuecomment-3344105405  

> Maybe it's worth adding a couple configure-only checks using different CMake versions.  
  
It definitely would be.

---

## Comment 8

> Username: Flamefire  
> Created_at: 2025-10-01 14:55:54 UTC  
> Url: https://github.com/boostorg/cmake/pull/90#issuecomment-3356837084  

Adding it to this repo: https://github.com/boostorg/cmake/pull/92  
  
It could make sense to have it in the main Boost repo such that it is run on every submodule update. But here it also makes sense to verify that changes like this one work across CMake versions

---
