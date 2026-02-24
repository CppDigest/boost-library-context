# #128 add coverity scan job and fix windows 64 builds [Closed]

> Username: jeking3  
> Created at: 2022-01-26 01:37:14 UTC  
> Updated at: 2022-02-02 22:34:15 UTC  
> Closed at: 2022-02-02 22:34:14 UTC  
> Url: https://github.com/boostorg/uuid/pull/128  

This closes #126

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-01-26 01:44:07 UTC  
> Updated_at: 2022-01-31 14:10:24 UTC  
> Url: https://github.com/boostorg/uuid/pull/128#issuecomment-1021775833  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/128?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#128](https://codecov.io/gh/boostorg/uuid/pull/128?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (856d5f9) into [develop](https://codecov.io/gh/boostorg/uuid/commit/0ee3b2b29e5a02ede29f364d005f9829f07f237e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0ee3b2b) will **increase** coverage by `1.33%`.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 856d5f9 differs from pull request most recent head 9305ce8. Consider uploading reports for the commit 9305ce8 to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/128/graphs/tree.svg?width=650&height=150&src=pr&token=6falIr5npV&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/uuid/pull/128?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #128      +/-   ##  
===========================================  
+ Coverage    60.44%   61.78%   +1.33%       
===========================================  
  Files           34       34                
  Lines         1388     1379       -9       
  Branches       593      578      -15       
===========================================  
+ Hits           839      852      +13       
+ Misses          55       51       -4       
+ Partials       494      476      -18       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/uuid/pull/128?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_hash.cpp](https://codecov.io/gh/boostorg/uuid/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2hhc2guY3Bw) | `22.22% <0.00%> (-14.15%)` | :arrow_down: |  
| [test/test\_uuid.cpp](https://codecov.io/gh/boostorg/uuid/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3V1aWQuY3Bw) | `41.90% <0.00%> (+0.95%)` | :arrow_up: |  
| [include/boost/uuid/uuid\_io.hpp](https://codecov.io/gh/boostorg/uuid/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91dWlkL3V1aWRfaW8uaHBw) | `60.00% <0.00%> (+1.05%)` | :arrow_up: |  
| [test/test\_uuid\_no\_simd.cpp](https://codecov.io/gh/boostorg/uuid/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3V1aWRfbm9fc2ltZC5jcHA=) | `36.25% <0.00%> (+1.25%)` | :arrow_up: |  
| [test/test\_uuid\_in\_map.cpp](https://codecov.io/gh/boostorg/uuid/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3V1aWRfaW5fbWFwLmNwcA==) | `16.66% <0.00%> (+1.28%)` | :arrow_up: |  
| [test/test\_random\_generator.cpp](https://codecov.io/gh/boostorg/uuid/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3JhbmRvbV9nZW5lcmF0b3IuY3Bw) | `27.08% <0.00%> (+2.08%)` | :arrow_up: |  
| [test/test\_io.cpp](https://codecov.io/gh/boostorg/uuid/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2lvLmNwcA==) | `21.35% <0.00%> (+2.91%)` | :arrow_up: |  
| [...nclude/boost/uuid/detail/random\_provider\_posix.ipp](https://codecov.io/gh/boostorg/uuid/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9yYW5kb21fcHJvdmlkZXJfcG9zaXguaXBw) | `82.14% <0.00%> (+3.57%)` | :arrow_up: |  
| [include/boost/uuid/random\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91dWlkL3JhbmRvbV9nZW5lcmF0b3IuaHBw) | `82.69% <0.00%> (+4.91%)` | :arrow_up: |  
| [test/test\_bench\_random.cpp](https://codecov.io/gh/boostorg/uuid/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2JlbmNoX3JhbmRvbS5jcHA=) | `78.37% <0.00%> (+9.45%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/128?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/128?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0ee3b2b...9305ce8](https://codecov.io/gh/boostorg/uuid/pull/128?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: jeking3  
> Created_at: 2022-01-31 14:15:57 UTC  
> Url: https://github.com/boostorg/uuid/pull/128#issuecomment-1025782939  

This PR has most things disabled as it was used to help test https://github.com/boostorg/boost-ci/pull/128; once that gets merged then all the build jobs will be restored.

---

## Comment 3

> Username: jeking3  
> Created_at: 2022-02-02 22:34:14 UTC  
> Url: https://github.com/boostorg/uuid/pull/128#issuecomment-1028422068  

Will open another one once the boost-ci changes go in for this.

---
