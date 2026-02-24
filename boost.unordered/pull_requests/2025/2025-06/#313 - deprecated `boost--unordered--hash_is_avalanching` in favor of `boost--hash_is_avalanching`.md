# #313 deprecated `boost::unordered::hash_is_avalanching` in favor of `boost::hash_is_avalanching` [Merged]

> Username: joaquintides  
> Created at: 2025-06-01 15:09:43 UTC  
> Updated at: 2025-06-02 10:44:39 UTC  
> Merged at: 2025-06-02 10:43:45 UTC  
> Closed at: 2025-06-02 10:43:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/313  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-06-01 15:18:11 UTC  
> Url: https://github.com/boostorg/unordered/pull/313#issuecomment-2927412037  

An automated preview of the documentation is available at [https://313.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://313.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2025-06-01 17:00:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/313#pullrequestreview-2885837168  

📁 include/boost/unordered/hash_traits.hpp

```diff
  15 |+ #include <boost/container_hash/hash_is_avalanching.hpp>
  16 |+ 
  17 |+ #if !defined(BOOST_ALLOW_DEPRECATED_HEADERS)&&!defined(BOOST_ALLOW_DEPRECATED)
```

> Username: pdimov  
> Created_at: 2025-06-01 17:00:54 UTC  
> Updated_at: 2025-06-01 17:00:55 UTC  
> Url: https://github.com/boostorg/unordered/pull/313#discussion_r2119336683  

Why not use BOOST_HEADER_DEPRECATED here? That's what it's for.

> Username: joaquintides  
> Created_at: 2025-06-01 17:53:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/313#discussion_r2119380299  

Because it's not only the header that is deprecated, but the use of `boost::unordered::hash_is_avalanching` too.

> Username: pdimov  
> Created_at: 2025-06-01 17:55:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/313#discussion_r2119382555  

But you can't use `boost::unordered::hash_is_avalanching` without including the header.


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2025-06-01 18:18:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/313#issuecomment-2927648417  

An automated preview of the documentation is available at [https://313.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://313.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2025-06-01 21:42:36 UTC  
> Updated_at: 2025-06-02 10:44:38 UTC  
> Url: https://github.com/boostorg/unordered/pull/313#issuecomment-2927943761  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/313?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.23%. Comparing base [(`164cbe4`)](https://app.codecov.io/gh/boostorg/unordered/commit/164cbe4e0ffbb870811a1f172af07b527bdc3ddc?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`a499f24`)](https://app.codecov.io/gh/boostorg/unordered/commit/a499f24eb53bb1f4259e677574ef261e7caca525?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/313/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/313?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #313   +/-   ##  
========================================  
  Coverage    98.23%   98.23%             
========================================  
  Files          161      161             
  Lines        22222    22224    +2       
========================================  
+ Hits         21830    21832    +2       
  Misses         392      392             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/unordered/pull/313?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/foa/core.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/313?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Ffoa%2Fcore.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb3JlLmhwcA==) | `99.54% <ø> (+<0.01%)` | :arrow_up: |  
| [test/unordered/hash\_is\_avalanching\_test.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/313?src=pr&el=tree&filepath=test%2Funordered%2Fhash_is_avalanching_test.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvaGFzaF9pc19hdmFsYW5jaGluZ190ZXN0LmNwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/313?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/313?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [164cbe4...a499f24](https://app.codecov.io/gh/boostorg/unordered/pull/313?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
