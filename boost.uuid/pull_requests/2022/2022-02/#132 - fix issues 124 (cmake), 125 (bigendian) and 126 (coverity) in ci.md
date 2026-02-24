# #132 fix issues 124 (cmake), 125 (bigendian) and 126 (coverity) in ci [Merged]

> Username: jeking3  
> Created at: 2022-02-04 01:06:28 UTC  
> Updated at: 2022-02-04 21:09:28 UTC  
> Merged at: 2022-02-04 21:09:20 UTC  
> Closed at: 2022-02-04 21:09:20 UTC  
> Url: https://github.com/boostorg/uuid/pull/132  

- add gha coverity scan (leveraging work I put into boost-ci)  
- add gha bigendian build (leveraging work I put into bdde and boost-ci)  
- tune codecov exclusionary list  
- fix windows gha address-models  
- fix cmake  
  
This closes #124   
This closes #125   
This closes #126

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-02-04 02:09:22 UTC  
> Updated_at: 2022-02-04 20:16:42 UTC  
> Url: https://github.com/boostorg/uuid/pull/132#issuecomment-1029574135  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/132?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#132](https://codecov.io/gh/boostorg/uuid/pull/132?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7d5f490) into [develop](https://codecov.io/gh/boostorg/uuid/commit/fb3ec7d83c5f3f14dc24e2a4765706fa29eb7a76?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (fb3ec7d) will **increase** coverage by `0.28%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/132/graphs/tree.svg?width=650&height=150&src=pr&token=6falIr5npV&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/uuid/pull/132?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #132      +/-   ##  
===========================================  
+ Coverage    60.44%   60.73%   +0.28%       
===========================================  
  Files           34       34                
  Lines         1388     1388                
  Branches       593      590       -3       
===========================================  
+ Hits           839      843       +4       
  Misses          55       55                
+ Partials       494      490       -4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/uuid/pull/132?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/uuid/uuid\_io.hpp](https://codecov.io/gh/boostorg/uuid/pull/132/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91dWlkL3V1aWRfaW8uaHBw) | `58.94% <0.00%> (ø)` | |  
| [include/boost/uuid/string\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/132/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91dWlkL3N0cmluZ19nZW5lcmF0b3IuaHBw) | `82.85% <0.00%> (+2.85%)` | :arrow_up: |  
| [include/boost/uuid/random\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/132/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91dWlkL3JhbmRvbV9nZW5lcmF0b3IuaHBw) | `81.48% <0.00%> (+3.70%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/132?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/132?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4cb8cb6...7d5f490](https://codecov.io/gh/boostorg/uuid/pull/132?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: jeking3  
> Created_at: 2022-02-04 19:59:14 UTC  
> Url: https://github.com/boostorg/uuid/pull/132#issuecomment-1030306636  

Coverage will omit test files once develop is merged to master.

---
