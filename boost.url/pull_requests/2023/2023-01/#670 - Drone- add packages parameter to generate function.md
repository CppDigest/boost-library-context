# #670 Drone: add packages parameter to generate function [Merged]

> Username: sdarwin  
> Created at: 2023-01-26 18:31:35 UTC  
> Updated at: 2023-03-02 16:07:05 UTC  
> Merged at: 2023-03-02 16:07:05 UTC  
> Closed at: 2023-03-02 16:07:05 UTC  
> Url: https://github.com/boostorg/url/pull/670  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-01-26 18:46:40 UTC  
> Updated_at: 2023-03-02 15:28:22 UTC  
> Url: https://github.com/boostorg/url/pull/670#issuecomment-1405446092  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/670?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#670](https://codecov.io/gh/boostorg/url/pull/670?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (4daa1cd) into [develop](https://codecov.io/gh/boostorg/url/commit/b0ad1c68a9dd527fc2fac3a2fcc1b737a904eeb4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b0ad1c6) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/670/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/670?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #670   +/-   ##  
========================================  
  Coverage    97.12%   97.12%             
========================================  
  Files          154      154             
  Lines         8432     8432             
========================================  
  Hits          8190     8190             
  Misses         242      242             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/670?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/670?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b0ad1c6...4daa1cd](https://codecov.io/gh/boostorg/url/pull/670?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-01-26 18:56:39 UTC  
> Url: https://github.com/boostorg/url/pull/670#issuecomment-1405458803  

GCOVR code coverage report [https://670.url.prtest.cppalliance.org/gcovr/index.html](https://670.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://670.url.prtest.cppalliance.org/genhtml/index.html](https://670.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://670.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://670.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2023-01-26 19:07:38 UTC  
> Url: https://github.com/boostorg/url/pull/670#issuecomment-1405473019  

Since people generally wouldn't have access to the `generate` function, wouldn't it be more general to have a `packages` parameter? I do understand how this can be a problem on windows where there's no apt-get but we'll have this problem anyway and the `packages` parameter could use `vcpkg` at some point when `b2` supports it. Maybe there's a `b2+vcpkg` workaround already and I don't know about it.

---

## Comment 4

> Username: sdarwin  
> Created_at: 2023-01-26 19:53:36 UTC  
> Url: https://github.com/boostorg/url/pull/670#issuecomment-1405551384  

> people won't have access  
  
Is the `generate` function aimed at all of boost, or is it mainly focused on cppalliance repositories?  I have been somewhat assuming that it's for cppalliance.   And so we could configure it in the most convenient way.    
  
Adding a `packages_linux` argument is another way to handle the current problem.  Yes.    If you prefer.    
  
Is that better or worse than a `library` argument?  Another existing problem is that beast isn't building all jobs using `generate` because beast has been a bit more customized.  So having the information about which library it is, opens the door to solve those issues, by doing some customized steps just for beast, inside of `generate` .

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2023-01-26 22:20:36 UTC  
> Url: https://github.com/boostorg/url/pull/670#issuecomment-1405748816  

> Is the generate function aimed at all of boost, or is it mainly focused on cppalliance repositories? I have been somewhat assuming that it's for cppalliance. And so we could configure it in the most convenient way.  
  
Yes. This would be a problem if other boost libraries start using it. I don't think a more general parameter is bad for cppalliance repositories either because it's much less work for us.  
  
For instance, many of not most repositories are going to depend on `zlib1g` from now on. `openssl` is also likely to come up frequently. It's much easier to let them have `packages=["zlib1g", "zlib1g-dev", "openssl"]` than to force them to ask us to keep adding new logic for `if library == "this library":` and `if library == "that library":` for each new library and wait for us to do that. Consumers write about the same amount of code, can add new dependencies without depending on us, and the generator doesn't have to change for each new library and contains less code.  
  
> Adding a packages_linux argument is another way to handle the current problem. Yes. If you prefer.  
  
Sure. We already have a list of packages to install in the generator loop so we just need to append the extra packages there. It's very simple code. If `packages_linux` is the only option for packages, I guess just `packages` is fine. We can later add `packages_vcpkg`, get the same packages on vcpkg, or use some map for <package manager, packages>.   
  
> Another existing problem is that beast isn't building all jobs using generate because beast has been a bit more customized. So having the information about which library it is, opens the door to solve those issues, by doing some customized steps just for beast, inside of generate .  
  
Yes. I remember beast being the one not in conformance with other repositories. What extra steps does beast have though? Can we name what these steps do without referring to "beast"? Some other repositories might need the extra steps at some point. On the other hand, if what beast is doing is the same steps only with different names, I tend to think it's easier to change the step name in the beast scripts. For now, if it's just the packages, that's less work for us. :)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-01-26 23:45:18 UTC  
> Url: https://github.com/boostorg/url/pull/670#issuecomment-1405821363  

GCOVR code coverage report [https://670.url.prtest.cppalliance.org/gcovr/index.html](https://670.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://670.url.prtest.cppalliance.org/genhtml/index.html](https://670.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://670.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://670.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-01-26 23:56:22 UTC  
> Url: https://github.com/boostorg/url/pull/670#issuecomment-1405829313  

GCOVR code coverage report [https://670.url.prtest.cppalliance.org/gcovr/index.html](https://670.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://670.url.prtest.cppalliance.org/genhtml/index.html](https://670.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://670.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://670.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-03-02 14:45:57 UTC  
> Url: https://github.com/boostorg/url/pull/670#issuecomment-1451986234  

GCOVR code coverage report [https://670.url.prtest.cppalliance.org/gcovr/index.html](https://670.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://670.url.prtest.cppalliance.org/genhtml/index.html](https://670.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://670.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://670.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
