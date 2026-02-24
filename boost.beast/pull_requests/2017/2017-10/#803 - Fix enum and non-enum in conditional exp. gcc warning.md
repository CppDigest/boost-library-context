# #803 Fix enum and non-enum in conditional exp. gcc warning [Closed]

> Username: hoditohod  
> Created at: 2017-10-03 10:09:24 UTC  
> Updated at: 2017-10-05 20:18:35 UTC  
> Closed at: 2017-10-05 20:18:34 UTC  
> Url: https://github.com/boostorg/beast/pull/803  

Fix "enumeral and non-enumeral type in conditional expression” GCC warning.   
Identical to line 364.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-10-03 10:23:37 UTC  
> Url: https://github.com/boostorg/beast/pull/803#issuecomment-333801091  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/803?src=pr&el=h1) Report  
> Merging [#803](https://codecov.io/gh/boostorg/beast/pull/803?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/7fe74b1bf544a64ecf8985fde44abf88f9902251?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/803/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/803?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #803   +/-   ##  
========================================  
  Coverage    95.71%   95.71%             
========================================  
  Files          105      105             
  Lines        10547    10547             
========================================  
  Hits         10095    10095             
  Misses         452      452  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/803?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.ipp](https://codecov.io/gh/boostorg/beast/pull/803?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmlwcA==) | `100% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/803?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/803?src=pr&el=footer). Last update [7fe74b1...8cb6f16](https://codecov.io/gh/boostorg/beast/pull/803?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-10-03 12:04:30 UTC  
> Url: https://github.com/boostorg/beast/pull/803#issuecomment-333821297  

I wonder why this warning doesn't appear on Travis (or does it?) Either way, I will merge it.

---

## Comment 3

> Username: hoditohod  
> Created_at: 2017-10-03 12:33:50 UTC  
> Url: https://github.com/boostorg/beast/pull/803#issuecomment-333827446  

GCC seems to be bit buggy with this warning:  
- I have 2 build configurations: one optimized release, and another with bullseye coverage for tests. The warning is only emitted with the second build, though the related compiler flags are the same  
- Beast is included as system include, gcc isn't supposed to emit a warning from beast headers, but it does  
- the warning itself doesn't state the exact line of the issue, only the list of instantiated templates. It took me some time to figure out which statement triggered it.  
  
I saw the same static_cast a few lines above, so I thought it might be worth to fix upstream, hence the PR.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2017-10-05 20:18:34 UTC  
> Url: https://github.com/boostorg/beast/pull/803#issuecomment-334580261  

Merged, thank you!

---
