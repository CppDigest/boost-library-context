# #161 Add fixed format printing to floff [Merged]

> Username: mborland  
> Created at: 2024-02-09 11:11:41 UTC  
> Updated at: 2024-02-15 11:06:47 UTC  
> Merged at: 2024-02-15 11:06:44 UTC  
> Closed at: 2024-02-15 11:06:44 UTC  
> Url: https://github.com/boostorg/charconv/pull/161  

Partially Addresses: #158

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-12 10:37:06 UTC  
> Updated_at: 2024-02-15 10:00:42 UTC  
> Url: https://github.com/boostorg/charconv/pull/161#issuecomment-1938420038  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/161?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: `17 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`02c3f97`)](https://app.codecov.io/gh/boostorg/charconv/commit/02c3f978195511d75697cfff57098a63e9f4ba8c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 88.64% compared to head [(`8556dfc`)](https://app.codecov.io/gh/boostorg/charconv/pull/161?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 92.09%.  
> Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/161/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/161?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #161      +/-   ##  
===========================================  
+ Coverage    88.64%   92.09%   +3.44%       
===========================================  
  Files           64       64                
  Lines         8162     8306     +144       
===========================================  
+ Hits          7235     7649     +414       
+ Misses         927      657     -270       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/charconv/pull/161?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/to\_chars.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/161?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzLmNwcA==) | `92.59% <ø> (+0.82%)` | :arrow_up: |  
| [src/to\_chars\_float\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/161?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzX2Zsb2F0X2ltcGwuaHBw) | `79.55% <100.00%> (-15.27%)` | :arrow_down: |  
| [test/github\_issue\_152.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/161?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTUyLmNwcA==) | `98.78% <100.00%> (+0.50%)` | :arrow_up: |  
| [test/github\_issue\_158.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/161?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTU4LmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/to\_chars\_float.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/161?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90b19jaGFyc19mbG9hdC5jcHA=) | `99.66% <ø> (-0.34%)` | :arrow_down: |  
| [test/to\_chars\_float\_STL\_comp.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/161?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90b19jaGFyc19mbG9hdF9TVExfY29tcC5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [test/to\_chars\_sprintf.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/161?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90b19jaGFyc19zcHJpbnRmLmNwcA==) | `96.70% <0.00%> (ø)` | |  
| [include/boost/charconv/detail/dragonbox/floff.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/161?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZHJhZ29uYm94L2Zsb2ZmLmhwcA==) | `72.18% <80.26%> (+28.47%)` | :arrow_up: |  
  
... and [3 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/charconv/pull/161/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/161?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/161?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [02c3f97...8556dfc](https://app.codecov.io/gh/boostorg/charconv/pull/161?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2024-02-15 11:06:40 UTC  
> Url: https://github.com/boostorg/charconv/pull/161#issuecomment-1945865765  

Merging as substantial incremental improvement. Replacing manual rounding with manipulation of floff to achieve our rounding goals to follow.

---
