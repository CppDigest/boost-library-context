# #626 decode_view snippets [Merged]

> Username: alandefreitas  
> Created at: 2022-11-09 01:27:48 UTC  
> Updated at: 2022-12-22 16:11:10 UTC  
> Merged at: 2022-11-10 05:35:02 UTC  
> Closed at: 2022-11-10 05:35:02 UTC  
> Url: https://github.com/boostorg/url/pull/626  

fix #625

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-11-09 01:46:28 UTC  
> Updated_at: 2022-11-10 05:29:52 UTC  
> Url: https://github.com/boostorg/url/pull/626#issuecomment-1308083823  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/626?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#626](https://codecov.io/gh/boostorg/url/pull/626?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9bf39cd) into [develop](https://codecov.io/gh/boostorg/url/commit/3ed169db365fbf70b9aa9ed507403456f07a40ec?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3ed169d) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/626/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/626?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #626   +/-   ##  
========================================  
  Coverage    96.47%   96.47%             
========================================  
  Files          138      138             
  Lines         6726     6726             
========================================  
  Hits          6489     6489             
  Misses         237      237             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/626?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/626?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3ed169d...9bf39cd](https://codecov.io/gh/boostorg/url/pull/626?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-10 01:50:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/626#pullrequestreview-1174973679  

📁 .github/workflows/ci.yml

```diff
 310 |         run: |
 311 |             echo GITHUB_REPOSITORY: $GITHUB_REPOSITORY
 312 |+             set -x
```

> Username: vinniefalco  
> Created_at: 2022-11-10 01:50:31 UTC  
> Url: https://github.com/boostorg/url/pull/626#discussion_r1018575582  

What's this set -x?


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-11-10 01:50:54 UTC  
> Url: https://github.com/boostorg/url/pull/626#issuecomment-1309657989  

I think this needs a rebase?

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-11-10 03:18:05 UTC  
> Url: https://github.com/boostorg/url/pull/626#issuecomment-1309713569  

> I think this needs a rebase?  
  
It's rebased. And nothing fails in drone.   
  
It's much worse than that. `b2` is not ignoring the warnings in GHA that it ignores in drone or even GHA in the last PR.  
  
I think there's something very wrong with the scripts we use in GHA. That's why I'm debugging it with `set -x`, etc...

---
