# #17 Enhance codebase line/branch coverage [Open]

> Username: poyenc  
> Created at: 2020-08-09 20:21:58 UTC  
> Updated at: 2022-04-27 22:20:07 UTC  
> Url: https://github.com/boostorg/tokenizer/pull/17  

The goal of code coverage is **90%**

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-08-10 04:09:42 UTC  
> Url: https://github.com/boostorg/tokenizer/pull/17#issuecomment-671153542  

# [Codecov](https://codecov.io/gh/boostorg/tokenizer/pull/17?src=pr&el=h1) Report  
> Merging [#17](https://codecov.io/gh/boostorg/tokenizer/pull/17?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/tokenizer/commit/1c8a8bfd3a8fc2071458884da23ef5ac3a778a0c&el=desc) will **increase** coverage by `21.97%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/tokenizer/pull/17/graphs/tree.svg?width=650&height=150&src=pr&token=sakwglU1PC)](https://codecov.io/gh/boostorg/tokenizer/pull/17?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff              @@  
##           develop      #17       +/-   ##  
============================================  
+ Coverage    70.95%   92.92%   +21.97%       
============================================  
  Files            3        3                 
  Lines          210      212        +2       
  Branches        74       66        -8       
============================================  
+ Hits           149      197       +48       
+ Misses          18        0       -18       
+ Partials        43       15       -28       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/tokenizer/pull/17?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/token\_iterator.hpp](https://codecov.io/gh/boostorg/tokenizer/pull/17/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC90b2tlbl9pdGVyYXRvci5ocHA=) | `92.30% <ø> (+21.93%)` | :arrow_up: |  
| [include/boost/token\_functions.hpp](https://codecov.io/gh/boostorg/tokenizer/pull/17/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC90b2tlbl9mdW5jdGlvbnMuaHBw) | `93.33% <100.00%> (+22.14%)` | :arrow_up: |  
| [include/boost/tokenizer.hpp](https://codecov.io/gh/boostorg/tokenizer/pull/17/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC90b2tlbml6ZXIuaHBw) | `83.33% <0.00%> (+16.66%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/tokenizer/pull/17?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/tokenizer/pull/17?src=pr&el=footer). Last update [1c8a8bf...63102a7](https://codecov.io/gh/boostorg/tokenizer/pull/17?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Changes requested]

> Username: aminya  
> Created_at: 2020-11-15 14:50:30 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/tokenizer/pull/17#pullrequestreview-530805822  

Could you break your changes into multiple commits? Each change should have a rational in their commit message.   
  
The commit which adds the tests is OK.

---

## Review 3 [Changes requested]

> Username: jeking3  
> Created_at: 2022-04-27 22:20:07 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/tokenizer/pull/17#pullrequestreview-955596152  

Please rebase and only include the test code changes, not the changes from #18.  When I tried to do this I got four test failures.

---
