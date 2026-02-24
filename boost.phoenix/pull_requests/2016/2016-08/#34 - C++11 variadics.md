# #34 C++11 variadics [Merged]

> Username: Flast  
> Created at: 2016-08-09 16:23:00 UTC  
> Updated at: 2016-08-13 02:11:22 UTC  
> Merged at: 2016-08-09 22:08:17 UTC  
> Closed at: 2016-08-09 22:08:17 UTC  
> Url: https://github.com/boostorg/phoenix/pull/34  

I know this PR is very very huge, but there is no new changes, except remove unused headers (325604c3f02af16be62a4623b3556c8f11659d48), regenerate preprocessed files (0d3abcc997190176be814cf9e74e086e76262c3d) and changelog (d85b8323e8fa61a82f80c9c2ef65e39c3540643a).  
  
This is not a breaking change, i.e. no user code should be changed.  
  
I tested on  
- GCC 6.1.1 20160721 (Red Hat 6.1.1-4)  
  - gnu++98 / 11 / 14 mode  
- clang 3.8.0  
  - gnu++98 / 11 / 14 mode  
- MSVC  
  - 8.0 (2005) - **failed**  
  - 9.0 (2008) - passed  
  - 10.0 (2010 SP1) - **failed**  
  - 11.0 (2012 u5) - passed  
  - 12.0 (2013 u5) - **failed**  
  - 14.0 (2015 u2) - **failed**  
  
I list all previous PRs for variadics.  
- #19 Prepare variadics  
- #21 Variadics: core/actor  
- #22 Restore copyright notice  
- #23 Variadics: core/call  
- #24 Implement C++11 variadic templates based function_equal  
- #25 Implement variadic based phx2_result  
- #26 variadics: core/function_eval  
- #27 C++11 variadics based core/expression  
- #29 variadics: prepare for other components  
- #30 variadics: bind component  
- #31 variadics: scope component  
- #32 variadics: expr macros  
  
I'll wait merging until it is approved.

---

## Comment 1

> Username: Flast  
> Created_at: 2016-08-09 22:15:23 UTC  
> Url: https://github.com/boostorg/phoenix/pull/34#issuecomment-238709246  

Newer MSVC shows much of regressions. So, I'll fix those until 1.62 beta close.

---

## Comment 2

> Username: Lastique  
> Created_at: 2016-08-10 08:06:39 UTC  
> Url: https://github.com/boostorg/phoenix/pull/34#issuecomment-238794956  

Please, revert the change from develop (or fix it if it's quicker) because it breaks a lot of Boost.Log tests and seeing test results during the release cycle is essential.  
  
http://www.boost.org/development/tests/develop/developer/output/teeks99-09e-win2012R2-64on64-boost-bin-v2-libs-log-test-~hdr-expressions-attr~hpp-test-msvc-12-0-dbg-adrs-mdl-64-thrd-mlt.html

---

## Comment 3

> Username: djowel  
> Created_at: 2016-08-10 08:53:18 UTC  
> Url: https://github.com/boostorg/phoenix/pull/34#issuecomment-238805385  

Ouch. @Flast, tell me if it's an easy fix, or we can revert for now.

---

## Comment 4

> Username: Flast  
> Created_at: 2016-08-10 12:09:27 UTC  
> Updated_at: 2016-08-10 12:10:23 UTC  
> Url: https://github.com/boostorg/phoenix/pull/34#issuecomment-238847547  

I think revert is best for now.

---

## Comment 5

> Username: Flast  
> Created_at: 2016-08-10 12:11:07 UTC  
> Url: https://github.com/boostorg/phoenix/pull/34#issuecomment-238847876  

done. I'll investigate this weekend.

---
