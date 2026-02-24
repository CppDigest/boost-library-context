# #62 fix uuid test bjam dependency declarations [Merged]

> Username: jeking3  
> Created at: 2018-03-26 15:16:57 UTC  
> Updated at: 2018-03-26 23:17:37 UTC  
> Merged at: 2018-03-26 23:17:32 UTC  
> Closed at: 2018-03-26 23:17:32 UTC  
> Url: https://github.com/boostorg/uuid/pull/62  

This fixes #60

---

## Comment 1

> Username: mclow  
> Created_at: 2018-03-26 15:25:19 UTC  
> Url: https://github.com/boostorg/uuid/pull/62#issuecomment-376206205  

This looks good to me; but I'll wait for the CI stuff to finish.

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-03-26 18:38:16 UTC  
> Url: https://github.com/boostorg/uuid/pull/62#issuecomment-376268631  

mingw failures on the develop branch are covered in #63

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-03-26 18:39:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/62#issuecomment-376269005  

Requested permission from release manager to include in 1.67.0 - waiting to hear.  In the meantime if the build for #63 passes I will merge that to develop and rebase this on top of it to clear out the mingw build issues in this PR.

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-03-26 18:45:29 UTC  
> Url: https://github.com/boostorg/uuid/pull/62#issuecomment-376270941  

Permission was granted, but it was requested I wait for a cycle of matrix tests on it in develop before merging to master for the release of 1.67.0.

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2018-03-26 20:05:36 UTC  
> Updated_at: 2018-03-26 21:59:46 UTC  
> Url: https://github.com/boostorg/uuid/pull/62#issuecomment-376294189  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/62?src=pr&el=h1) Report  
> Merging [#62](https://codecov.io/gh/boostorg/uuid/pull/62?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/uuid/commit/d37088ab078f0f96cf964b80d195d8678ca456e8?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/62/graphs/tree.svg?src=pr&width=650&token=6falIr5npV&height=150)](https://codecov.io/gh/boostorg/uuid/pull/62?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #62   +/-   ##  
========================================  
  Coverage    78.34%   78.34%             
========================================  
  Files           13       13             
  Lines          605      605             
  Branches       156      156             
========================================  
  Hits           474      474             
  Misses          17       17             
  Partials       114      114  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/62?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/62?src=pr&el=footer). Last update [d37088a...c4abb78](https://codecov.io/gh/boostorg/uuid/pull/62?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
