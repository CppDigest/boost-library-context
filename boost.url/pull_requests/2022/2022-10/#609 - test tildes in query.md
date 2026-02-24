# #609 test tildes in query [Merged]

> Username: alandefreitas  
> Created at: 2022-10-25 01:35:21 UTC  
> Updated at: 2022-10-26 22:41:28 UTC  
> Merged at: 2022-10-26 22:41:27 UTC  
> Closed at: 2022-10-26 22:41:27 UTC  
> Url: https://github.com/boostorg/url/pull/609  

fix #497

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2022-10-25 01:35:42 UTC  
> Updated_at: 2022-10-25 01:35:51 UTC  
> Url: https://github.com/boostorg/url/pull/609#issuecomment-1289861177  

This is already working. So the PR only includes the test.

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-10-25 01:43:55 UTC  
> Url: https://github.com/boostorg/url/pull/609#issuecomment-1289866032  

GCOVR code coverage report [https://609.url.prtest.cppalliance.org/gcovr/index.html](https://609.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://609.url.prtest.cppalliance.org/genhtml/index.html](https://609.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-10-25 01:54:22 UTC  
> Updated_at: 2022-10-26 22:35:19 UTC  
> Url: https://github.com/boostorg/url/pull/609#issuecomment-1289871796  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/609?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#609](https://codecov.io/gh/boostorg/url/pull/609?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e81f041) into [develop](https://codecov.io/gh/boostorg/url/commit/3e09e9a2487dab1326504757c9203a666df05b61?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3e09e9a) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/609/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/609?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #609   +/-   ##  
========================================  
  Coverage    96.47%   96.47%             
========================================  
  Files          138      138             
  Lines         6727     6727             
========================================  
  Hits          6490     6490             
  Misses         237      237             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/609?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/609?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3e09e9a...e81f041](https://codecov.io/gh/boostorg/url/pull/609?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-26 16:07:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/609#pullrequestreview-1156862486  

📁 test/unit/parse.cpp

```diff
  23 |+         // issue 497
  24 |+         {
  25 |+             url_view u = parse_uri_reference("?~").value();
```

> Username: vinniefalco  
> Created_at: 2022-10-26 16:07:28 UTC  
> Url: https://github.com/boostorg/url/pull/609#discussion_r1005884109  

well if this fails then it will throw, and screw up CI. Use `BOOST_TEST_NO_THROW` or check the `result`.


---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-10-26 16:07:51 UTC  
> Url: https://github.com/boostorg/url/pull/609#issuecomment-1292278283  

Read my comment, and act on it if you think it is important. Then merge it when you feel it is ready.

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-10-26 21:58:40 UTC  
> Url: https://github.com/boostorg/url/pull/609#issuecomment-1292701527  

GCOVR code coverage report [https://609.url.prtest.cppalliance.org/gcovr/index.html](https://609.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://609.url.prtest.cppalliance.org/genhtml/index.html](https://609.url.prtest.cppalliance.org/genhtml/index.html)

---
