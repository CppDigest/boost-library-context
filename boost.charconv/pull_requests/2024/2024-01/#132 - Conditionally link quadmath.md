# #132 Conditionally link quadmath [Merged]

> Username: mborland  
> Created at: 2024-01-23 07:47:55 UTC  
> Updated at: 2024-01-29 08:38:35 UTC  
> Merged at: 2024-01-29 08:38:32 UTC  
> Closed at: 2024-01-29 08:38:32 UTC  
> Url: https://github.com/boostorg/charconv/pull/132  

Closes: #130

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-23 08:19:23 UTC  
> Updated_at: 2024-01-29 08:25:45 UTC  
> Url: https://github.com/boostorg/charconv/pull/132#issuecomment-1905521732  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/132?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`b404767`)](https://app.codecov.io/gh/cppalliance/charconv/commit/b404767a5745791afe906d4a65e04d035bea1b6c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.29% compared to head [(`cc4a09a`)](https://app.codecov.io/gh/cppalliance/charconv/pull/132?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.29%.  
> Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/132/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/132?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #132   +/-   ##  
========================================  
  Coverage    88.29%   88.29%             
========================================  
  Files           56       56             
  Lines         7919     7919             
========================================  
  Hits          6992     6992             
  Misses         927      927             
```  
  
  
[see 2 files with indirect coverage changes](https://app.codecov.io/gh/cppalliance/charconv/pull/132/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/132?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/132?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [b404767...cc4a09a](https://app.codecov.io/gh/cppalliance/charconv/pull/132?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---

## Comment 2

> Username: anarthal  
> Created_at: 2024-01-24 15:51:44 UTC  
> Url: https://github.com/boostorg/charconv/pull/132#issuecomment-1908410026  

I've attempted this but it still fails. See https://drone.cpp.al/boostorg/mysql/484/2/3.  
  
`find_library` is failing to detect `quadmath` support. From what I've seen in my system, `quadmath` doesn't seem to be located in the standard search paths (probably because it's an "internal" gcc library?).  
  
From a quick search and inspecting your Jamfiles, I'd suggest to use the same approach in cmake. See https://github.com/sunqm/qcint/blob/master/cmake/FindQUADMATH.cmake for a possibility.  
  
To locally reproduce the build environment I referenced above, I suggest you to use Docker. This build is using the image `ghcr.io/anarthal-containers/build-clang14`. `docker run -it ghcr.io/anarthal-containers/build-clang14` can do it.

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-01-24 16:18:27 UTC  
> Url: https://github.com/boostorg/charconv/pull/132#issuecomment-1908463363  

Is this the correct logic? Shouldn't Quadmath be linked only when needed, not whenever it's present?

---

## Comment 4

> Username: mborland  
> Created_at: 2024-01-24 16:21:45 UTC  
> Url: https://github.com/boostorg/charconv/pull/132#issuecomment-1908470765  

> Is this the correct logic? Shouldn't Quadmath be linked only when needed, not whenever it's present?  
  
I thought so since CMake defaults to building with "gnu++". It's nearly the same issue that we ran into with the CMake testing suite.

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-01-24 16:22:51 UTC  
> Url: https://github.com/boostorg/charconv/pull/132#issuecomment-1908472946  

Never mind, I see that Jamfile does the same thing. I suppose `config/has_float128.cpp` can be reused on the CMake side as well.

---

## Comment 6

> Username: mborland  
> Created_at: 2024-01-25 10:31:18 UTC  
> Url: https://github.com/boostorg/charconv/pull/132#issuecomment-1909858112  

> I've attempted this but it still fails. See https://drone.cpp.al/boostorg/mysql/484/2/3.  
>   
> `find_library` is failing to detect `quadmath` support. From what I've seen in my system, `quadmath` doesn't seem to be located in the standard search paths (probably because it's an "internal" gcc library?).  
>   
> From a quick search and inspecting your Jamfiles, I'd suggest to use the same approach in cmake. See https://github.com/sunqm/qcint/blob/master/cmake/FindQUADMATH.cmake for a possibility.  
>   
> To locally reproduce the build environment I referenced above, I suggest you to use Docker. This build is using the image `ghcr.io/anarthal-containers/build-clang14`. `docker run -it ghcr.io/anarthal-containers/build-clang14` can do it.  
  
Using this docker image I am able to compile and run your demo program with latest commit pulled in. In the cmake testing setting cmake extensions to off didn't work either so a macro was added to compile without any of the quadmath support. That macro is now defined if quadmath is not found, and also gives the user the option if they want. Seems to also work fine with the cmake based testing in the CI here. @anarthal let me know if your CI goes green with this now.

---

## Comment 7

> Username: anarthal  
> Created_at: 2024-01-26 12:57:09 UTC  
> Url: https://github.com/boostorg/charconv/pull/132#issuecomment-1912029807  

It does build. However, it seems to be always detecting that `libquadmath` is not there. From my tests, it looks like you could define BOOST_CHARCONV_NO_QUADMATH to 1 unconditionally and yield the same results.  
  
I don't need any __float128 support, so it's up to you if you want to resolve this like this or not.

---
