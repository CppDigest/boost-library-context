# #111 Require C++11 and remove support and use of `std::auto_ptr` [Merged]

> Username: Flamefire  
> Created at: 2022-09-14 09:23:12 UTC  
> Updated at: 2022-09-14 14:54:50 UTC  
> Merged at: 2022-09-14 14:54:47 UTC  
> Closed at: 2022-09-14 14:54:47 UTC  
> Url: https://github.com/boostorg/locale/pull/111  

As previously announced this drops support for C++ prior to C++11 which allows to get rid of a few dependencies and conditional code and compatibility macros.  
  
Going forward this will also simplify the code easing development especially due to use of (unconditional) move semantics.  
  
It also drops the use and support of `std::auto_ptr` avoiding deprecating warnings and pitfalls.  
  
Closes #90

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-09-14 11:17:04 UTC  
> Updated_at: 2022-09-14 11:21:52 UTC  
> Url: https://github.com/boostorg/locale/pull/111#issuecomment-1246616239  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/111?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#111](https://codecov.io/gh/boostorg/locale/pull/111?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (564a422) into [develop](https://codecov.io/gh/boostorg/locale/commit/903724cd033ffdafad490ecaa5896ecb5595ae8e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (903724c) will **increase** coverage by `0.04%`.  
> The diff coverage is `77.56%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/111/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/111?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #111      +/-   ##  
===========================================  
+ Coverage    80.69%   80.73%   +0.04%       
===========================================  
  Files           73       73                
  Lines         5790     5766      -24       
===========================================  
- Hits          4672     4655      -17       
+ Misses        1118     1111       -7       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/111?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/gnu\_gettext.hpp](https://codecov.io/gh/boostorg/locale/pull/111/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ251X2dldHRleHQuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/localization\_backend.hpp](https://codecov.io/gh/boostorg/locale/pull/111/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbG9jYWxpemF0aW9uX2JhY2tlbmQuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/util.hpp](https://codecov.io/gh/boostorg/locale/pull/111/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC5ocHA=) | `0.00% <ø> (ø)` | |  
| [src/boost/locale/shared/generator.cpp](https://codecov.io/gh/boostorg/locale/pull/111/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvZ2VuZXJhdG9yLmNwcA==) | `79.56% <ø> (ø)` | |  
| [src/boost/locale/shared/mo\_lambda.hpp](https://codecov.io/gh/boostorg/locale/pull/111/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvbW9fbGFtYmRhLmhwcA==) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/std/collate.cpp](https://codecov.io/gh/boostorg/locale/pull/111/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zdGQvY29sbGF0ZS5jcHA=) | `27.27% <0.00%> (ø)` | |  
| [test/test\_formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/111/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/test\_utf.cpp](https://codecov.io/gh/boostorg/locale/pull/111/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3V0Zi5jcHA=) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/util/numeric.hpp](https://codecov.io/gh/boostorg/locale/pull/111/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL251bWVyaWMuaHBw) | `76.10% <20.00%> (ø)` | |  
| [src/boost/locale/shared/mo\_lambda.cpp](https://codecov.io/gh/boostorg/locale/pull/111/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvbW9fbGFtYmRhLmNwcA==) | `60.34% <50.00%> (ø)` | |  
| ... and [17 more](https://codecov.io/gh/boostorg/locale/pull/111/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/111?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/111?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [903724c...564a422](https://codecov.io/gh/boostorg/locale/pull/111?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
