# #55 Do not extend the pool on each allocation [Open]

> Username: orgads  
> Created at: 2023-06-22 13:12:42 UTC  
> Updated at: 2025-09-22 06:21:18 UTC  
> Url: https://github.com/boostorg/pool/pull/55  

Reported in Boost mailing list: https://lists.boost.org/Archives/boost/2023/06/254750.php  
  
Commit 951ca57 changed malloc_need_resize() to use set_next_size(), which sets start_size in addition to setting next_size. this causes repeated use of purge_memory() to allocate 2x size after every use.  
  
Fixes #54

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-06-24 20:24:36 UTC  
> Updated_at: 2024-09-17 14:08:11 UTC  
> Url: https://github.com/boostorg/pool/pull/55#issuecomment-1605717167  

## [Codecov](https://app.codecov.io/gh/boostorg/pool/pull/55?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.69%. Comparing base [(`8ec1be1`)](https://app.codecov.io/gh/boostorg/pool/commit/8ec1be1e82ba559744ecfa3c6ec13f71f9c175cc?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`82b22e3`)](https://app.codecov.io/gh/boostorg/pool/commit/82b22e305c312b0111237e93646b1e87037c13d8?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/pool/pull/55/graphs/tree.svg?width=650&height=150&src=pr&token=blmk8CZxg2&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/pool/pull/55?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #55      +/-   ##  
===========================================  
- Coverage    93.71%   93.69%   -0.03%       
===========================================  
  Files            9        9                
  Lines          589      587       -2       
===========================================  
- Hits           552      550       -2       
  Misses          37       37                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/pool/pull/55?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/pool/pool.hpp](https://app.codecov.io/gh/boostorg/pool/pull/55?src=pr&el=tree&filepath=include%2Fboost%2Fpool%2Fpool.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9wb29sL3Bvb2wuaHBw) | `96.58% <100.00%> (-0.03%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/pool/pull/55?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/pool/pull/55?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8ec1be1...82b22e3](https://app.codecov.io/gh/boostorg/pool/pull/55?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Changes requested]

> Username: jeking3  
> Created_at: 2025-01-03 23:17:07 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/pool/pull/55#pullrequestreview-2529819615  

Rebase on current develop for a full CI suite run.

📁 include/boost/pool/pool.hpp

```diff
 306 |+       else
 307 |+         return;
 308 |+       next_size = min BOOST_PREVENT_MACRO_SUBSTITUTION(nnext_size, max_chunks());
```

> Username: jeking3  
> Created_at: 2025-01-03 23:16:35 UTC  
> Updated_at: 2025-01-03 23:17:08 UTC  
> Url: https://github.com/boostorg/pool/pull/55#discussion_r1902230668  

`set_next_size` sets more than next_size, should we be calling `set_next_size(nnext_size)` here?

> Username: orgads  
> Created_at: 2025-01-04 16:23:04 UTC  
> Url: https://github.com/boostorg/pool/pull/55#discussion_r1903121621  

But that's exactly what the bug is about. I added the bug description to the commit message and the PR now.


---

## Comment 3

> Username: orgads  
> Created_at: 2025-01-04 16:23:23 UTC  
> Url: https://github.com/boostorg/pool/pull/55#issuecomment-2571347341  

Rebased.

---

## Comment 4

> Username: orgads  
> Created_at: 2025-01-04 19:00:52 UTC  
> Url: https://github.com/boostorg/pool/pull/55#issuecomment-2571383981  

The failure is due to quota limit on codecov, unrelated to my change.

---

## Comment 5

> Username: ptlopez  
> Created_at: 2025-09-22 06:21:18 UTC  
> Url: https://github.com/boostorg/pool/pull/55#issuecomment-3317112138  

Will this come in any time soon?

---
