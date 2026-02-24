# #667 path-rootless considers invalid segments [Merged]

> Username: alandefreitas  
> Created at: 2023-01-24 20:50:25 UTC  
> Updated at: 2024-02-21 00:55:52 UTC  
> Merged at: 2023-01-24 23:29:33 UTC  
> Closed at: 2023-01-24 23:29:33 UTC  
> Url: https://github.com/boostorg/url/pull/667  

fix #665

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-24 20:52:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/667#pullrequestreview-1268277748  

📁 test/unit/parse.cpp

```diff
  35 |+             {
  36 |+                 auto r = parse_uri_reference("A:\"");
  37 |+                 BOOST_TEST_THROWS(r.value(), system_error);
```

> Username: vinniefalco  
> Created_at: 2023-01-24 20:52:21 UTC  
> Url: https://github.com/boostorg/url/pull/667#discussion_r1085908508  

Great :)


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2023-01-24 20:52:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/667#pullrequestreview-1268277845  

📁 test/unit/parse.cpp

```diff
  25 |             auto r = parse_uri_reference("?~");
  26 |-             BOOST_TEST_NO_THROW(r);
  26 |+             BOOST_TEST_NO_THROW(r.value());
```

> Username: vinniefalco  
> Created_at: 2023-01-24 20:52:26 UTC  
> Updated_at: 2023-01-24 20:52:27 UTC  
> Url: https://github.com/boostorg/url/pull/667#discussion_r1085908618  

oops

> Username: alandefreitas  
> Created_at: 2023-01-24 20:59:47 UTC  
> Url: https://github.com/boostorg/url/pull/667#discussion_r1085917231  

:)


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-01-24 21:09:09 UTC  
> Updated_at: 2023-01-24 21:10:37 UTC  
> Url: https://github.com/boostorg/url/pull/667#issuecomment-1402667983  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/667?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#667](https://codecov.io/gh/boostorg/url/pull/667?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8d93bea) into [develop](https://codecov.io/gh/boostorg/url/commit/c3a70873d63affc5735f918dfbf94416799d7399?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c3a7087) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/667/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/667?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #667   +/-   ##  
========================================  
  Coverage    97.13%   97.13%             
========================================  
  Files          152      152             
  Lines         8435     8435             
========================================  
  Hits          8193     8193             
  Misses         242      242             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/667?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...clude/boost/url/rfc/detail/impl/hier\_part\_rule.ipp](https://codecov.io/gh/boostorg/url/pull/667?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2RldGFpbC9pbXBsL2hpZXJfcGFydF9ydWxlLmlwcA==) | `94.73% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/667?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/667?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c3a7087...8d93bea](https://codecov.io/gh/boostorg/url/pull/667?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-01-24 21:10:05 UTC  
> Url: https://github.com/boostorg/url/pull/667#issuecomment-1402669126  

GCOVR code coverage report [https://667.url.prtest.cppalliance.org/gcovr/index.html](https://667.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://667.url.prtest.cppalliance.org/genhtml/index.html](https://667.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://667.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://667.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
