# #28 Use `unsigned char` as the underlying type for the tribool state [Open]

> Username: Lastique  
> Created at: 2023-05-03 17:18:20 UTC  
> Updated at: 2025-07-25 23:32:44 UTC  
> Url: https://github.com/boostorg/logic/pull/28  

This makes `sizeof(tribool) == 1`, which is similar to `sizeof(bool)` on most platforms. This is only enabled when C++11 scoped enums are supported.

---

## Comment 1

> Username: mclow  
> Created_at: 2023-05-03 17:19:53 UTC  
> Url: https://github.com/boostorg/logic/pull/28#issuecomment-1533423087  

Note that sizeof(bool) == 4 on most unix systems.

---

## Comment 2

> Username: Lastique  
> Created_at: 2023-05-03 18:05:20 UTC  
> Updated_at: 2023-05-03 18:08:14 UTC  
> Url: https://github.com/boostorg/logic/pull/28#issuecomment-1533486725  

You mean legacy UNIX'es that are now dead? I'm only aware of Solaris on Sparc where this is true. On Linux and, I think, all BSDs, `sizeof(bool)` is 1. Windows, too.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-05-04 02:58:48 UTC  
> Url: https://github.com/boostorg/logic/pull/28#issuecomment-1534015492  

## [Codecov](https://app.codecov.io/gh/boostorg/logic/pull/28?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#28](https://app.codecov.io/gh/boostorg/logic/pull/28?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d53263d) into [develop](https://app.codecov.io/gh/boostorg/logic/commit/145778490c2d332c1411df6a5274a4b53ec3e091?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1457784) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/logic/pull/28/graphs/tree.svg?width=650&height=150&src=pr&token=2RCqoPxHln&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/logic/pull/28?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #28   +/-   ##  
========================================  
  Coverage    97.72%   97.72%             
========================================  
  Files            2        2             
  Lines          132      132             
========================================  
  Hits           129      129             
  Misses           3        3             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/logic/pull/28?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/logic/tribool.hpp](https://app.codecov.io/gh/boostorg/logic/pull/28?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2dpYy90cmlib29sLmhwcA==) | `98.27% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/logic/pull/28?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/logic/pull/28?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1457784...d53263d](https://app.codecov.io/gh/boostorg/logic/pull/28?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: jeking3  
> Created_at: 2025-07-25 21:02:58 UTC  
> Url: https://github.com/boostorg/logic/pull/28#issuecomment-3120348451  

@Lastique can you rebase on develop to get the full set of CI to run on this?

---

## Comment 5

> Username: Lastique  
> Created_at: 2025-07-25 23:32:44 UTC  
> Url: https://github.com/boostorg/logic/pull/28#issuecomment-3120677920  

> @Lastique can you rebase on develop to get the full set of CI to run on this?  
  
Done.

---
