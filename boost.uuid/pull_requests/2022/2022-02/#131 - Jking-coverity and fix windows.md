# #131 Jking/coverity and fix windows [Closed]

> Username: jeking3  
> Created at: 2022-02-03 02:17:26 UTC  
> Updated at: 2022-02-04 21:09:51 UTC  
> Closed at: 2022-02-04 01:03:44 UTC  
> Url: https://github.com/boostorg/uuid/pull/131  

test coverage fixups

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-02-03 03:30:40 UTC  
> Updated_at: 2022-02-03 15:59:43 UTC  
> Url: https://github.com/boostorg/uuid/pull/131#issuecomment-1028569767  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/131?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#131](https://codecov.io/gh/boostorg/uuid/pull/131?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c9b5dcb) into [develop](https://codecov.io/gh/boostorg/uuid/commit/0ee3b2b29e5a02ede29f364d005f9829f07f237e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0ee3b2b) will **increase** coverage by `21.52%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/131/graphs/tree.svg?width=650&height=150&src=pr&token=6falIr5npV&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/uuid/pull/131?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff              @@  
##           develop     #131       +/-   ##  
============================================  
+ Coverage    60.44%   81.97%   +21.52%       
============================================  
  Files           34       15       -19       
  Lines         1388      699      -689       
  Branches       593      155      -438       
============================================  
- Hits           839      573      -266       
+ Misses          55       40       -15       
+ Partials       494       86      -408       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/uuid/pull/131?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...nclude/boost/uuid/detail/random\_provider\_posix.ipp](https://codecov.io/gh/boostorg/uuid/pull/131/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9yYW5kb21fcHJvdmlkZXJfcG9zaXguaXBw) | `62.16% <0.00%> (-16.41%)` | :arrow_down: |  
| [include/boost/uuid/detail/random\_provider.hpp](https://codecov.io/gh/boostorg/uuid/pull/131/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9yYW5kb21fcHJvdmlkZXIuaHBw) | `86.66% <0.00%> (-13.34%)` | :arrow_down: |  
| [include/boost/uuid/basic\_name\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/131/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91dWlkL2Jhc2ljX25hbWVfZ2VuZXJhdG9yLmhwcA==) | `64.40% <0.00%> (-4.69%)` | :arrow_down: |  
| [include/boost/uuid/uuid.hpp](https://codecov.io/gh/boostorg/uuid/pull/131/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91dWlkL3V1aWQuaHBw) | `95.45% <0.00%> (-4.55%)` | :arrow_down: |  
| [include/boost/uuid/detail/uuid\_x86.ipp](https://codecov.io/gh/boostorg/uuid/pull/131/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC91dWlkX3g4Ni5pcHA=) | `96.42% <0.00%> (-3.58%)` | :arrow_down: |  
| [include/boost/uuid/detail/md5.hpp](https://codecov.io/gh/boostorg/uuid/pull/131/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9tZDUuaHBw) | `86.92% <0.00%> (-1.74%)` | :arrow_down: |  
| [test/test\_tagging.cpp](https://codecov.io/gh/boostorg/uuid/pull/131/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3RhZ2dpbmcuY3Bw) | | |  
| [test/test\_md5.cpp](https://codecov.io/gh/boostorg/uuid/pull/131/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X21kNS5jcHA=) | | |  
| [test/test\_uuid\_in\_map.cpp](https://codecov.io/gh/boostorg/uuid/pull/131/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3V1aWRfaW5fbWFwLmNwcA==) | | |  
| [test/test\_include1.cpp](https://codecov.io/gh/boostorg/uuid/pull/131/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2luY2x1ZGUxLmNwcA==) | | |  
| ... and [18 more](https://codecov.io/gh/boostorg/uuid/pull/131/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/131?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/131?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0ee3b2b...c9b5dcb](https://codecov.io/gh/boostorg/uuid/pull/131?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
