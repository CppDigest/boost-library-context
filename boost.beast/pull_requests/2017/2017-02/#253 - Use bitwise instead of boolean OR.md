# #253 Use bitwise instead of boolean OR [Closed]

> Username: nbougalis  
> Created at: 2017-02-05 22:45:22 UTC  
> Updated at: 2017-07-25 17:44:18 UTC  
> Closed at: 2017-02-27 17:07:55 UTC  
> Url: https://github.com/boostorg/beast/pull/253  



---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-02-05 22:46:13 UTC  
> Url: https://github.com/boostorg/beast/pull/253#issuecomment-277556458  

Suggested commit message:  
```  
Check ostream modifier correctly  
```  
Also CHANGELOG.md needs an updating

---

## Comment 2

> Username: codecov-io  
> Created_at: 2017-02-05 23:16:58 UTC  
> Updated_at: 2017-02-05 23:40:21 UTC  
> Url: https://github.com/boostorg/beast/pull/253#issuecomment-277558507  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/253?src=pr&el=h1) Report  
> Merging [#253](https://codecov.io/gh/vinniefalco/Beast/pull/253?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/06f74f05f7de51d7f791a17c2b06840183332cbe?src=pr&el=desc) will **not impact** coverage.  
  
  
```diff  
@@           Coverage Diff           @@  
##           master     #253   +/-   ##  
=======================================  
  Coverage   94.94%   94.94%             
=======================================  
  Files          87       87             
  Lines        6229     6229             
=======================================  
  Hits         5914     5914             
  Misses        315      315  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/253?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/253?src=pr&el=footer). Last update [06f74f0...61e323b](https://codecov.io/gh/vinniefalco/Beast/compare/06f74f05f7de51d7f791a17c2b06840183332cbe...61e323b03217fe578826ea4d55258823ac3cfed8?el=footer&src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: ximinez  
> Created_at: 2017-02-06 15:49:12 UTC  
> Url: https://github.com/boostorg/beast/pull/253#issuecomment-277722761  

s/OR/AND/

---

## Comment 4

> Username: nbougalis  
> Created_at: 2017-02-06 16:03:09 UTC  
> Url: https://github.com/boostorg/beast/pull/253#issuecomment-277727226  

D'oh. `AND`, yes.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2017-02-06 21:00:35 UTC  
> Url: https://github.com/boostorg/beast/pull/253#issuecomment-277811747  

Just a reminder, we need to analyze the calls to `log.flush()` to determine if they are still necessary (since I added them to work around this `unitbuf` bug):  
```  
extras\beast\unit_test\reporter.hpp(286): os_.flush();  
extras\beast\unit_test\suite.hpp(356): suite_.log.flush();  
extras\beast\unit_test\suite.hpp(397): suite_.log.flush();  
```

---

## Comment 6

> Username: nbougalis  
> Created_at: 2017-02-14 21:37:40 UTC  
> Url: https://github.com/boostorg/beast/pull/253#issuecomment-279843943  

With this change, the calls to `flush` are no longer needed.

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2017-02-14 21:39:39 UTC  
> Url: https://github.com/boostorg/beast/pull/253#issuecomment-279844457  

Please remove the calls to `flush` then!

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2017-02-27 17:07:55 UTC  
> Url: https://github.com/boostorg/beast/pull/253#issuecomment-282783493  

Fixed in **1.0.0-b29**

---
