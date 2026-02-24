# #410 no more error_code& [Closed]

> Username: alandefreitas  
> Created at: 2022-08-16 06:24:08 UTC  
> Updated at: 2022-08-30 21:04:05 UTC  
> Closed at: 2022-08-18 16:06:42 UTC  
> Url: https://github.com/boostorg/url/pull/410  

fix #368

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-08-16 07:11:30 UTC  
> Updated_at: 2022-08-17 01:49:06 UTC  
> Url: https://github.com/boostorg/url/pull/410#issuecomment-1216237524  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/410?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#410](https://codecov.io/gh/CPPAlliance/url/pull/410?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (015f8ec) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/68c53b6192805fe67bc723eff4c1a5ccdb8b3687?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (68c53b6) will **increase** coverage by `0.29%`.  
> The diff coverage is `98.78%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/410/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/410?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #410      +/-   ##  
===========================================  
+ Coverage    97.89%   98.19%   +0.29%       
===========================================  
  Files          132      132                
  Lines         6281     6259      -22       
===========================================  
- Hits          6149     6146       -3       
+ Misses         132      113      -19       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/410?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/pct\_encoded\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/410/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGN0X2VuY29kZWRfdmlldy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_base.hpp](https://codecov.io/gh/CPPAlliance/url/pull/410/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/CPPAlliance/url/pull/410/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `98.86% <92.30%> (+0.08%)` | :arrow_up: |  
| [include/boost/url/detail/impl/any\_path\_iter.ipp](https://codecov.io/gh/CPPAlliance/url/pull/410/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvYW55X3BhdGhfaXRlci5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/impl/any\_query\_iter.ipp](https://codecov.io/gh/CPPAlliance/url/pull/410/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvYW55X3F1ZXJ5X2l0ZXIuaXBw) | `94.14% <100.00%> (+1.13%)` | :arrow_up: |  
| [...url/detail/impl/segments\_encoded\_iterator\_impl.ipp](https://codecov.io/gh/CPPAlliance/url/pull/410/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvc2VnbWVudHNfZW5jb2RlZF9pdGVyYXRvcl9pbXBsLmlwcA==) | `95.31% <100.00%> (ø)` | |  
| [...e/boost/url/detail/impl/segments\_iterator\_impl.ipp](https://codecov.io/gh/CPPAlliance/url/pull/410/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvc2VnbWVudHNfaXRlcmF0b3JfaW1wbC5pcHA=) | `95.31% <100.00%> (ø)` | |  
| [include/boost/url/grammar/impl/not\_empty\_rule.hpp](https://codecov.io/gh/CPPAlliance/url/pull/410/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL25vdF9lbXB0eV9ydWxlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/grammar/impl/optional\_rule.hpp](https://codecov.io/gh/CPPAlliance/url/pull/410/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL29wdGlvbmFsX3J1bGUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/grammar/impl/parse.hpp](https://codecov.io/gh/CPPAlliance/url/pull/410/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL3BhcnNlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| ... and [18 more](https://codecov.io/gh/CPPAlliance/url/pull/410/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/410?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/410?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [68c53b6...015f8ec](https://codecov.io/gh/CPPAlliance/url/pull/410?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-16 13:51:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/410#pullrequestreview-1074157900  

📁 include/boost/url/detail/impl/any_path_iter.ipp

```diff
  78 |+     auto rn = urls::validate_pct_encoding(
  79 |+         s, pchars, {});
  80 |+     if(rn.has_error())
```

> Username: vinniefalco  
> Created_at: 2022-08-16 13:51:41 UTC  
> Url: https://github.com/boostorg/url/pull/410#discussion_r946806171  

I write `if( ! rn )`


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-16 13:52:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/410#pullrequestreview-1074159689  

📁 include/boost/url/url_base.hpp

```diff
1450 |     friend
1451 |-     void
1451 |+     error_code
```

> Username: vinniefalco  
> Created_at: 2022-08-16 13:52:43 UTC  
> Updated_at: 2022-08-16 13:52:44 UTC  
> Url: https://github.com/boostorg/url/pull/410#discussion_r946807391  

Well this isn't the same as returning `result` now is it? Because the user has no easy way to convert the error into an exception. @pdimov ?

> Username: pdimov  
> Created_at: 2022-08-16 16:10:38 UTC  
> Updated_at: 2022-08-16 16:10:47 UTC  
> Url: https://github.com/boostorg/url/pull/410#discussion_r946982151  

`result<void>`

> Username: alandefreitas  
> Created_at: 2022-08-16 22:27:56 UTC  
> Url: https://github.com/boostorg/url/pull/410#discussion_r947299517  

Mmm... No problem. I didn't think `result<void>` would be desirable. I just never saw it.


---
