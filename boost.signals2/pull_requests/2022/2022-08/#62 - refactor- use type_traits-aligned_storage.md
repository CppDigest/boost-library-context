# #62 refactor: use type_traits/aligned_storage [Merged]

> Username: fanquake  
> Created at: 2022-08-23 14:52:39 UTC  
> Updated at: 2022-08-24 14:00:36 UTC  
> Merged at: 2022-08-24 14:00:09 UTC  
> Closed at: 2022-08-24 14:00:10 UTC  
> Url: https://github.com/boostorg/signals2/pull/62  

`boost/aligned_storage.hpp` just includes #includes <boost/type_traits/aligned_storage.hpp>.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-08-23 15:21:48 UTC  
> Url: https://github.com/boostorg/signals2/pull/62#issuecomment-1224226671  

# [Codecov](https://codecov.io/gh/boostorg/signals2/pull/62?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#62](https://codecov.io/gh/boostorg/signals2/pull/62?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1259a19) into [develop](https://codecov.io/gh/boostorg/signals2/commit/49ed1573fa91bb8c60d0a20a5d5e36d8b6d94ea9?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (49ed157) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #62   +/-   ##  
========================================  
  Coverage    48.26%   48.26%             
========================================  
  Files           47       47             
  Lines         2215     2215             
  Branches      1096     1096             
========================================  
  Hits          1069     1069             
  Misses         126      126             
  Partials      1020     1020             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/signals2/pull/62?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...clude/boost/signals2/detail/slot\_call\_iterator.hpp](https://codecov.io/gh/boostorg/signals2/pull/62/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9zaWduYWxzMi9kZXRhaWwvc2xvdF9jYWxsX2l0ZXJhdG9yLmhwcA==) | `71.42% <ø> (ø)` | |  
| [test/signal\_n\_test.cpp](https://codecov.io/gh/boostorg/signals2/pull/62/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9zaWduYWxfbl90ZXN0LmNwcA==) | `33.33% <0.00%> (ø)` | |  
  
Help us with your feedback. Take ten seconds to tell us [how you rate us](https://about.codecov.io/nps?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Have a feature suggestion? [Share it here.](https://app.codecov.io/gh/feedback/?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)

---
