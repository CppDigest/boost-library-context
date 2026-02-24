# #689 support BOOST_URL_DISABLE_THREADS [Merged]

> Username: alandefreitas  
> Created at: 2023-02-20 23:26:54 UTC  
> Updated at: 2023-02-23 09:46:17 UTC  
> Merged at: 2023-02-22 01:11:04 UTC  
> Closed at: 2023-02-22 01:11:04 UTC  
> Url: https://github.com/boostorg/url/pull/689  

fix #684

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2023-02-20 23:29:20 UTC  
> Url: https://github.com/boostorg/url/pull/689#issuecomment-1437667369  

Where do we detect single-threaded toolchains? The user has to set this manually?

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2023-02-20 23:43:32 UTC  
> Url: https://github.com/boostorg/url/pull/689#issuecomment-1437672781  

@ropieur could you try this branch? This implementation uses [`BOOST_SYSTEM_DISABLE_THREADS`](https://github.com/boostorg/system/commit/53c00841fc0d892bf43cda60e3ea2f05c4362b32) as a reference.

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-02-20 23:44:37 UTC  
> Url: https://github.com/boostorg/url/pull/689#issuecomment-1437673132  

GCOVR code coverage report [https://689.url.prtest.cppalliance.org/gcovr/index.html](https://689.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://689.url.prtest.cppalliance.org/genhtml/index.html](https://689.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://689.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://689.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2023-02-20 23:46:09 UTC  
> Updated_at: 2023-02-21 23:52:03 UTC  
> Url: https://github.com/boostorg/url/pull/689#issuecomment-1437673645  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/689?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#689](https://codecov.io/gh/boostorg/url/pull/689?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (58ed304) into [develop](https://codecov.io/gh/boostorg/url/commit/b49fd368fd5df46bcae5185fdbf7b963125de041?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b49fd36) will **decrease** coverage by `0.02%`.  
> The diff coverage is `94.44%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/689/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/689?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #689      +/-   ##  
===========================================  
- Coverage    97.13%   97.12%   -0.02%       
===========================================  
  Files          152      152                
  Lines         8441     8445       +4       
===========================================  
+ Hits          8199     8202       +3       
- Misses         242      243       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/689?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/grammar/detail/recycled.hpp](https://codecov.io/gh/boostorg/url/pull/689?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9kZXRhaWwvcmVjeWNsZWQuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/recycled.hpp](https://codecov.io/gh/boostorg/url/pull/689?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9yZWN5Y2xlZC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/detail/impl/recycled.ipp](https://codecov.io/gh/boostorg/url/pull/689?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9kZXRhaWwvaW1wbC9yZWN5Y2xlZC5pcHA=) | `95.65% <92.85%> (-4.35%)` | :arrow_down: |  
| [include/boost/url/grammar/impl/recycled.hpp](https://codecov.io/gh/boostorg/url/pull/689?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL3JlY3ljbGVkLmhwcA==) | `87.93% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/689?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/689?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b49fd36...58ed304](https://codecov.io/gh/boostorg/url/pull/689?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2023-02-20 23:48:14 UTC  
> Url: https://github.com/boostorg/url/pull/689#issuecomment-1437674346  

@sdarwin any ideas to improve that?  
  
https://github.com/alandefreitas/url/blob/a50a01a75a0d246874646423a5e1a2a3c2cdcf8b/.drone.star#L28-L33

---

## Review 6 [Commented]

> Username: pdimov  
> Created_at: 2023-02-21 18:09:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/689#pullrequestreview-1307939424  

📁 include/boost/url/grammar/detail/impl/recycled.ipp

```diff
  94 |     std::lock_guard<
  95 |         std::mutex> m(all_reports_.m);
  96 |+ #endif
```

> Username: pdimov  
> Created_at: 2023-02-21 18:09:12 UTC  
> Url: https://github.com/boostorg/url/pull/689#discussion_r1113411742  

You can just make `count` and `bytes` atomic instead of using a mutex.


---

## Review 7 [Commented]

> Username: pdimov  
> Created_at: 2023-02-21 18:11:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/689#pullrequestreview-1307942436  

📁 include/boost/url/grammar/recycled.hpp

```diff
  92 |         U* next = nullptr;
  93 |+ 
  94 |+ #if !defined(BOOST_URL_DISABLE_THREADS)
```

> Username: pdimov  
> Created_at: 2023-02-21 18:11:29 UTC  
> Updated_at: 2023-02-21 18:33:51 UTC  
> Url: https://github.com/boostorg/url/pull/689#discussion_r1113413834  

This is unnecessary. Single threaded C++11 platforms have a working `<atomic>`.

> Username: alandefreitas  
> Created_at: 2023-02-21 18:28:05 UTC  
> Updated_at: 2023-02-21 18:28:06 UTC  
> Url: https://github.com/boostorg/url/pull/689#discussion_r1113428438  

`<atomic>`


---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-02-21 23:19:46 UTC  
> Url: https://github.com/boostorg/url/pull/689#issuecomment-1439219057  

GCOVR code coverage report [https://689.url.prtest.cppalliance.org/gcovr/index.html](https://689.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://689.url.prtest.cppalliance.org/genhtml/index.html](https://689.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://689.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://689.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-02-21 23:45:00 UTC  
> Url: https://github.com/boostorg/url/pull/689#issuecomment-1439234874  

GCOVR code coverage report [https://689.url.prtest.cppalliance.org/gcovr/index.html](https://689.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://689.url.prtest.cppalliance.org/genhtml/index.html](https://689.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://689.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://689.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Review 10 [Commented]

> Username: pdimov  
> Created_at: 2023-02-22 01:15:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/689#pullrequestreview-1308382101  

📁 include/boost/url/grammar/detail/impl/recycled.ipp

```diff
  71 |+     {}
  72 |+ 
  73 |+     std::size_t new_bytes = a.bytes.fetch_add(n);
```

> Username: pdimov  
> Created_at: 2023-02-22 01:15:12 UTC  
> Updated_at: 2023-02-22 01:15:13 UTC  
> Url: https://github.com/boostorg/url/pull/689#discussion_r1113726645  

fetch_add returns the old value, not the new one. `a.bytes += n`

> Username: alandefreitas  
> Created_at: 2023-02-22 02:41:48 UTC  
> Updated_at: 2023-02-22 02:41:49 UTC  
> Url: https://github.com/boostorg/url/pull/689#discussion_r1113768240  

:man_facepalming:

> Username: alandefreitas  
> Created_at: 2023-02-22 02:45:53 UTC  
> Updated_at: 2023-02-22 02:45:54 UTC  
> Url: https://github.com/boostorg/url/pull/689#discussion_r1113770046  

This?  
  
https://github.com/boostorg/url/pull/691


---

## Comment 11

> Username: ropieur  
> Created_at: 2023-02-23 09:46:17 UTC  
> Url: https://github.com/boostorg/url/pull/689#issuecomment-1441462100  

> @ropieur could you try this branch? This implementation uses [`BOOST_SYSTEM_DISABLE_THREADS`](https://github.com/boostorg/system/commit/53c00841fc0d892bf43cda60e3ea2f05c4362b32) as a reference.  
  
I will have to check internally with my colleagues how we can proceed next week when I am back at work, because we are building boost as a 3rdparty all at once. This will require from us to merge your changes with boost 1.81.0 and tweak our building procedure. I'll keep you posted.

---
