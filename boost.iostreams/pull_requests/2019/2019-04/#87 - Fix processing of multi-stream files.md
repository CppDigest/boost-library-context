# #87 Fix processing of multi-stream files [Merged]

> Username: gfrontera  
> Created at: 2019-04-23 17:58:44 UTC  
> Updated at: 2019-05-24 16:51:29 UTC  
> Merged at: 2019-05-24 12:29:55 UTC  
> Closed at: 2019-05-24 12:29:55 UTC  
> Url: https://github.com/boostorg/iostreams/pull/87  

Fixes issue #86

---

## Comment 1

> Username: gfrontera  
> Created_at: 2019-04-24 09:08:28 UTC  
> Url: https://github.com/boostorg/iostreams/pull/87#issuecomment-486136505  

Perhaps there is a better fix but this patch fixes the problem for me.

---

## Comment 2

> Username: jeking3  
> Created_at: 2019-05-02 21:02:03 UTC  
> Url: https://github.com/boostorg/iostreams/pull/87#issuecomment-488830990  

Two things:  
  
1. Is there a test that reasonably exercised this?  I'm thinking no, since the original code was not failing tests, so we could use a test.  
  
2. Rebase on develop and force push so it builds clean with CI fixes.  Thanks.

---

## Review 3 [Changes requested]

> Username: jeking3  
> Created_at: 2019-05-02 21:02:28 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/iostreams/pull/87#pullrequestreview-233236069  

We could use a test with input that leverages multi-stream files.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2019-05-09 11:38:37 UTC  
> Updated_at: 2019-05-10 05:00:38 UTC  
> Url: https://github.com/boostorg/iostreams/pull/87#issuecomment-490867979  

# [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/87?src=pr&el=h1) Report  
> Merging [#87](https://codecov.io/gh/boostorg/iostreams/pull/87?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/iostreams/commit/50b4f003e5205db1243e4d2469e6f795fb57dc45?src=pr&el=desc) will **decrease** coverage by `0.09%`.  
> The diff coverage is `93.33%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/iostreams/pull/87/graphs/tree.svg?width=650&token=LBEfwtNfca&height=150&src=pr)](https://codecov.io/gh/boostorg/iostreams/pull/87?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop      #87     +/-   ##  
==========================================  
- Coverage    69.04%   68.94%   -0.1%       
==========================================  
  Files           80       80               
  Lines         3437     3439      +2       
  Branches      1025     1027      +2       
==========================================  
- Hits          2373     2371      -2       
- Misses         451      452      +1       
- Partials       613      616      +3  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/iostreams/pull/87?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/iostreams/filter/bzip2.hpp](https://codecov.io/gh/boostorg/iostreams/pull/87/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZmlsdGVyL2J6aXAyLmhwcA==) | `70.27% <93.33%> (-0.57%)` | :arrow_down: |  
| [src/bzip2.cpp](https://codecov.io/gh/boostorg/iostreams/pull/87/diff?src=pr&el=tree#diff-c3JjL2J6aXAyLmNwcA==) | `84.9% <0%> (-5.67%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/iostreams/pull/87?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/87?src=pr&el=footer). Last update [50b4f00...328a826](https://codecov.io/gh/boostorg/iostreams/pull/87?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 5

> Username: gfrontera  
> Created_at: 2019-05-09 16:05:12 UTC  
> Url: https://github.com/boostorg/iostreams/pull/87#issuecomment-490966039  

Thank you for your feedback, @jeking3.  
It turns out a test _did_ exist. However, the size of the compressed multi-stream was too small for the bug to manifest. The whole multi-stream could fit in the buffer at the same time, and in that case the existing implementation worked correctly.  
I have just increased the size of the compressed multi-stream (approximately 5x), so now the test fails without this proposed fix.

---

## Comment 6

> Username: gfrontera  
> Created_at: 2019-05-24 10:52:26 UTC  
> Url: https://github.com/boostorg/iostreams/pull/87#issuecomment-495572306  

Please @jeking3, could you re-review this pull request to see if the changes made are sufficient?

---

## Review 7 [Approved]

> Username: jeking3  
> Created_at: 2019-05-24 12:28:59 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/iostreams/pull/87#pullrequestreview-241711486  

Looks like it now:  
  
    meet the postcondition requiring that either i1 == i2 or o1 == o2  
  
described in the related issue.

---
