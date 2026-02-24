# #609 Fix integer overflow #608 [Closed]

> Username: pauldreik  
> Created at: 2021-08-17 06:11:12 UTC  
> Updated at: 2021-08-18 07:13:30 UTC  
> Closed at: 2021-08-17 19:18:54 UTC  
> Url: https://github.com/boostorg/json/pull/609  

I added the offending test case to the known crashing inputs so it is tested in CI by the existing fuzz job.  
  
I verified the code change solved the problem.  
  
I also fixed a few more places in the same file which had the same pattern.  
  
fixes #608

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-08-17 06:36:07 UTC  
> Url: https://github.com/boostorg/json/pull/609#issuecomment-900033700  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/609?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#609](https://codecov.io/gh/boostorg/json/pull/609?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ae711ee) into [develop](https://codecov.io/gh/boostorg/json/commit/478fe8d19750f1b3d3f5697c05469bf1530272fa?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (478fe8d) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head ae711ee differs from pull request most recent head 1912ae7. Consider uploading reports for the commit 1912ae7 to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/609/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/609?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #609   +/-   ##  
========================================  
  Coverage    99.05%   99.05%             
========================================  
  Files           68       68             
  Lines         6114     6114             
========================================  
  Hits          6056     6056             
  Misses          58       58             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/609?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser\_impl.hpp](https://codecov.io/gh/boostorg/json/pull/609/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `99.19% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/609?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/609?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [478fe8d...1912ae7](https://codecov.io/gh/boostorg/json/pull/609?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: grisumbras  
> Created_at: 2021-08-17 19:18:53 UTC  
> Url: https://github.com/boostorg/json/pull/609#issuecomment-900566395  

Merged via 3f48a274589aac06280b2c1b0e3d70796cac4b05

---
