# #130 fix test jamfile referring to BOOST_ROOT instead of relative paths [Merged]

> Username: jeking3  
> Created at: 2022-02-02 22:33:28 UTC  
> Updated at: 2022-02-03 01:59:16 UTC  
> Merged at: 2022-02-03 01:59:12 UTC  
> Closed at: 2022-02-03 01:59:12 UTC  
> Url: https://github.com/boostorg/uuid/pull/130  

Referring to `BOOST_ROOT` for dependencies is bad; if "b2 headers" had not been run yet at the top level and you tried to build uuid separately, the test jamfile was wrong.  With this fix it can build independently and seed the appropriate headers without a full build.  
  
Without:  
```  
Performing configuration checks  
  
    - default address-model    : 64-bit [1]  
    - default architecture     : x86 [1]  
    - symlinks supported       : yes  
error: Unable to find file or target named  
error:     '/boost/boost/uuid/uuid.hpp'  
error: referred to from project at  
error:     'test'  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-02-02 22:57:51 UTC  
> Url: https://github.com/boostorg/uuid/pull/130#issuecomment-1028435900  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/130?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#130](https://codecov.io/gh/boostorg/uuid/pull/130?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1462b83) into [develop](https://codecov.io/gh/boostorg/uuid/commit/0ee3b2b29e5a02ede29f364d005f9829f07f237e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0ee3b2b) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/130/graphs/tree.svg?width=650&height=150&src=pr&token=6falIr5npV&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/uuid/pull/130?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #130   +/-   ##  
========================================  
  Coverage    60.44%   60.44%             
========================================  
  Files           34       34             
  Lines         1388     1388             
  Branches       593      593             
========================================  
  Hits           839      839             
  Misses          55       55             
  Partials       494      494             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/130?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/130?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0ee3b2b...1462b83](https://codecov.io/gh/boostorg/uuid/pull/130?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
