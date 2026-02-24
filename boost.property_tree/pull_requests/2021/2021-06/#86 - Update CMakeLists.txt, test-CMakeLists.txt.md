# #86 Update CMakeLists.txt, test/CMakeLists.txt [Merged]

> Username: pdimov  
> Created at: 2021-06-08 00:31:59 UTC  
> Updated at: 2021-06-08 09:06:48 UTC  
> Merged at: 2021-06-08 09:06:38 UTC  
> Closed at: 2021-06-08 09:06:38 UTC  
> Url: https://github.com/boostorg/property_tree/pull/86  

In the root CML, this moves the options/folders outside the BOOST_SUPERPROJECT_VERSION case. In tests/CML, it attaches the tests to the `tests` target, which will be the Boost-wide convention for building the test executables. They are still left attached to the `all` target as well, because I didn't know if that was relied upon. If not, the test executables should use EXCLUDE_FROM_ALL.

---

## Comment 1

> Username: madmongo1  
> Created_at: 2021-06-08 09:06:24 UTC  
> Url: https://github.com/boostorg/property_tree/pull/86#issuecomment-856597189  

It probably is relied upon for now in the CI, but shouldn't be.

---
