# #2493 Switch to mac-11 in GHA [Merged]

> Username: sdarwin  
> Created at: 2022-08-04 12:55:04 UTC  
> Updated at: 2022-10-02 13:32:40 UTC  
> Merged at: 2022-10-02 13:32:39 UTC  
> Closed at: 2022-10-02 13:32:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2493  



---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2022-09-24 04:23:25 UTC  
> Updated_at: 2022-09-24 04:24:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2493#issuecomment-1256852541  

This is bad - we're getting a SEGFAULT from this change?  Only TSAN, but that should warrant an investigation.  
  
@vinniefalco @madmongo1 did you ever run into that in local development?  
  
Either case, I think we can merge this, but we need to investigate what's going on in the `write` test.

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2022-10-02 12:55:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2493#issuecomment-1264637206  

It looks like #2521 might have fixed this.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-10-02 13:17:46 UTC  
> Updated_at: 2022-10-02 13:17:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2493#issuecomment-1264642161  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2493?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2493](https://codecov.io/gh/boostorg/beast/pull/2493?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (417fc95) into [develop](https://codecov.io/gh/boostorg/beast/commit/00293a6adb5383fe14217a8916dd7ff79a857483?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (00293a6) will **increase** coverage by `0.03%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2493/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2493?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2493      +/-   ##  
===========================================  
+ Coverage    94.75%   94.79%   +0.03%       
===========================================  
  Files          152      152                
  Lines        11868    12240     +372       
===========================================  
+ Hits         11246    11603     +357       
- Misses         622      637      +15       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2493?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2493/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.38% <0.00%> (-0.78%)` | :arrow_down: |  
| [include/boost/beast/core/impl/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/2493/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvbXVsdGlfYnVmZmVyLmhwcA==) | `97.94% <0.00%> (+0.35%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2493?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2493?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [00293a6...417fc95](https://codecov.io/gh/boostorg/beast/pull/2493?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
