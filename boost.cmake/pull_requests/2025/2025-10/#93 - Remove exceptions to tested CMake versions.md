# #93 Remove exceptions to tested CMake versions [Merged]

> Username: Flamefire  
> Created at: 2025-10-07 08:57:21 UTC  
> Updated at: 2025-10-29 11:55:37 UTC  
> Merged at: 2025-10-29 11:36:27 UTC  
> Closed at: 2025-10-29 11:36:27 UTC  
> Url: https://github.com/boostorg/cmake/pull/93  

Those PRs have been merged:  
  
- [x] msm: https://github.com/boostorg/msm/pull/122  
- [x] pfr https://github.com/boostorg/pfr/pull/223  
- [x] pfr https://github.com/boostorg/pfr/pull/224  
  
So the exceptions can be removed.

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-10-07 11:15:43 UTC  
> Url: https://github.com/boostorg/cmake/pull/93#issuecomment-3376431901  

Looks like https://github.com/boostorg/pfr/pull/224 hasn't been merged.  
  
Do you look for `cmake_minimum_required` in `test/CMakeLists.txt`? If so, maybe that should be added to PFR's test/CML, instead of conditioning the tests.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-10-07 11:22:54 UTC  
> Updated_at: 2025-10-07 11:23:08 UTC  
> Url: https://github.com/boostorg/cmake/pull/93#issuecomment-3376453055  

> Looks like [boostorg/pfr#224](https://github.com/boostorg/pfr/pull/224) hasn't been merged.  
  
I added that when the CI failed and a checkbox to the description here.  
  
> Do you look for `cmake_minimum_required` in `test/CMakeLists.txt`? If so, maybe that should be added to PFR's test/CML, instead of conditioning the tests.  
  
Not yet. Usually `cmake_minimum_required` should be done at the top-level CML anyway.      
As for PFR: only a subset of the tests require C++20. In B2 those won't be build in C++17 mode or below. So that change mirrors that, building as much as possible. I don't see a need to error out when building the tests with an older CMake version

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-10-10 11:53:57 UTC  
> Url: https://github.com/boostorg/cmake/pull/93#issuecomment-3389691466  

Please retarget against cmake_versions.yml.

---

## Comment 4

> Username: apolukhin  
> Created_at: 2025-10-29 07:19:18 UTC  
> Url: https://github.com/boostorg/cmake/pull/93#issuecomment-3460151973  

Merged the https://github.com/boostorg/pfr/pull/224

---
