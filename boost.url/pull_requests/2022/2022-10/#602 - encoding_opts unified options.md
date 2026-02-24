# #602 encoding_opts unified options [Merged]

> Username: alandefreitas  
> Created at: 2022-10-15 00:57:23 UTC  
> Updated at: 2022-12-22 16:16:07 UTC  
> Merged at: 2022-10-24 19:43:37 UTC  
> Closed at: 2022-10-24 19:43:37 UTC  
> Url: https://github.com/boostorg/url/pull/602  

~~Work in progress~~  
  
This PR includes encoding_opts unified options and other smaller bugs we had to fix along the way

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2022-10-15 01:00:36 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1279618719  

I was covering everything but that revealed some errors that were problematic. One of them was related to the reverse path comparison algorithm, since we changed normalization a few times but not that algorithm that compares urls as if they were normalized. The good thing I already know how to fix the problem and we know have tools like decode_view that make it much easier.

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-10-15 01:06:43 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1279620108  

An automated preview of the documentation is available at [https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-10-15 01:10:02 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1279621006  

GCOVR code coverage report [https://602.url.prtest.cppalliance.org/gcovr/index.html](https://602.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://602.url.prtest.cppalliance.org/genhtml/index.html](https://602.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-10-15 01:19:38 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1279623158  

An automated preview of the documentation is available at [https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-10-18 19:41:49 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1282915065  

An automated preview of the documentation is available at [https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-10-18 19:43:37 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1282917406  

GCOVR code coverage report [https://602.url.prtest.cppalliance.org/gcovr/index.html](https://602.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://602.url.prtest.cppalliance.org/genhtml/index.html](https://602.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-10-18 20:38:28 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1282976114  

An automated preview of the documentation is available at [https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-10-18 20:40:32 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1282978200  

GCOVR code coverage report [https://602.url.prtest.cppalliance.org/gcovr/index.html](https://602.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://602.url.prtest.cppalliance.org/genhtml/index.html](https://602.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-10-18 20:49:39 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1282988311  

GCOVR code coverage report [https://602.url.prtest.cppalliance.org/gcovr/index.html](https://602.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://602.url.prtest.cppalliance.org/genhtml/index.html](https://602.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-10-18 20:56:00 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1282994028  

GCOVR code coverage report [https://602.url.prtest.cppalliance.org/gcovr/index.html](https://602.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://602.url.prtest.cppalliance.org/genhtml/index.html](https://602.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-10-18 20:56:43 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1282994687  

An automated preview of the documentation is available at [https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-10-18 21:26:46 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1283024601  

An automated preview of the documentation is available at [https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-10-18 21:28:41 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1283026195  

GCOVR code coverage report [https://602.url.prtest.cppalliance.org/gcovr/index.html](https://602.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://602.url.prtest.cppalliance.org/genhtml/index.html](https://602.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-21 17:14:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/602#pullrequestreview-1151304452  

📁 include/boost/url/params_ref.hpp

```diff
 123 |+     params_ref(
 124 |+         params_ref const& other,
 125 |+         encoding_opts opt) noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-10-21 17:14:36 UTC  
> Url: https://github.com/boostorg/url/pull/602#discussion_r1002019289  

Needs a javadoc and tests


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-21 17:14:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/602#pullrequestreview-1151304689  

📁 include/boost/url/params_view.hpp

```diff
 106 |+     params_view(
 107 |+         params_view const& other,
 108 |+         encoding_opts opt);
```

> Username: vinniefalco  
> Created_at: 2022-10-21 17:14:49 UTC  
> Updated_at: 2022-10-21 17:14:50 UTC  
> Url: https://github.com/boostorg/url/pull/602#discussion_r1002019516  

Should be noexcept, needs a javadoc and tests


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-21 17:15:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/602#pullrequestreview-1151304900  

📁 include/boost/url/params_view.hpp

```diff
 167 |+     params_view(
 168 |+         string_view s,
 169 |+         encoding_opts opt);
```

> Username: vinniefalco  
> Created_at: 2022-10-21 17:15:01 UTC  
> Url: https://github.com/boostorg/url/pull/602#discussion_r1002019691  

needs a javadoc and tests


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-21 17:15:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/602#pullrequestreview-1151305313  

📁 include/boost/url/rfc/impl/pct_encoded_rule.hpp

```diff
  31 |     // VFALCO TODO
  32 |-     // opt.plus_to_space?
  32 |+     // opt.space_as_plus?
```

> Username: vinniefalco  
> Created_at: 2022-10-21 17:15:24 UTC  
> Url: https://github.com/boostorg/url/pull/602#discussion_r1002020090  

Why does this say "to do?"


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-21 17:15:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/602#pullrequestreview-1151305689  

📁 include/boost/url/url_base.hpp

```diff
2081 |+     BOOST_URL_DECL
2082 |+     params_ref
2083 |+     params(encoding_opts opt) noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-10-21 17:15:43 UTC  
> Updated_at: 2022-10-21 17:15:44 UTC  
> Url: https://github.com/boostorg/url/pull/602#discussion_r1002020459  

need javadoc and tests


---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2022-10-24 15:57:55 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1289250769  

An automated preview of the documentation is available at [https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2022-10-24 15:59:24 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1289252832  

GCOVR code coverage report [https://602.url.prtest.cppalliance.org/gcovr/index.html](https://602.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://602.url.prtest.cppalliance.org/genhtml/index.html](https://602.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2022-10-24 16:52:35 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1289319287  

GCOVR code coverage report [https://602.url.prtest.cppalliance.org/gcovr/index.html](https://602.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://602.url.prtest.cppalliance.org/genhtml/index.html](https://602.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2022-10-24 16:53:03 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1289319923  

An automated preview of the documentation is available at [https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2022-10-24 16:59:51 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1289329038  

GCOVR code coverage report [https://602.url.prtest.cppalliance.org/gcovr/index.html](https://602.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://602.url.prtest.cppalliance.org/genhtml/index.html](https://602.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2022-10-24 17:07:47 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1289337656  

An automated preview of the documentation is available at [https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2022-10-24 18:13:12 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1289407915  

An automated preview of the documentation is available at [https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2022-10-24 18:13:45 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1289408514  

GCOVR code coverage report [https://602.url.prtest.cppalliance.org/gcovr/index.html](https://602.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://602.url.prtest.cppalliance.org/genhtml/index.html](https://602.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 27

> Username: codecov[bot]  
> Created_at: 2022-10-24 18:26:05 UTC  
> Updated_at: 2022-10-24 18:43:52 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1289422010  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/602?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#602](https://codecov.io/gh/boostorg/url/pull/602?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (503b3c0) into [develop](https://codecov.io/gh/boostorg/url/commit/91eb8109a38b59dc4a72f0354d30422a7a2ec3f7?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (91eb810) will **decrease** coverage by `0.32%`.  
> The diff coverage is `98.99%`.  
  
> :exclamation: Current head 503b3c0 differs from pull request most recent head 8b956d8. Consider uploading reports for the commit 8b956d8 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/602/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/602?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #602      +/-   ##  
===========================================  
- Coverage    96.80%   96.47%   -0.33%       
===========================================  
  Files          137      138       +1       
  Lines         6729     6727       -2       
===========================================  
- Hits          6514     6490      -24       
- Misses         215      237      +22       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/602?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/normalize.hpp](https://codecov.io/gh/boostorg/url/pull/602/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL25vcm1hbGl6ZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/url\_impl.hpp](https://codecov.io/gh/boostorg/url/pull/602/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3VybF9pbXBsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/pct\_string\_view.ipp](https://codecov.io/gh/boostorg/url/pull/602/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wY3Rfc3RyaW5nX3ZpZXcuaXBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params\_ref.hpp](https://codecov.io/gh/boostorg/url/pull/602/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3JlZi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params\_view.hpp](https://codecov.io/gh/boostorg/url/pull/602/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/pct\_string\_view.hpp](https://codecov.io/gh/boostorg/url/pull/602/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGN0X3N0cmluZ192aWV3LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/rfc/impl/pct\_encoded\_rule.hpp](https://codecov.io/gh/boostorg/url/pull/602/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvcGN0X2VuY29kZWRfcnVsZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_base.hpp](https://codecov.io/gh/boostorg/url/pull/602/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/url\_view\_base.ipp](https://codecov.io/gh/boostorg/url/pull/602/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlld19iYXNlLmlwcA==) | `97.95% <60.00%> (-0.69%)` | :arrow_down: |  
| [include/boost/url/detail/impl/normalize.ipp](https://codecov.io/gh/boostorg/url/pull/602/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvbm9ybWFsaXplLmlwcA==) | `92.07% <98.79%> (-7.29%)` | :arrow_down: |  
| ... and [25 more](https://codecov.io/gh/boostorg/url/pull/602/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/602?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/602?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [91eb810...8b956d8](https://codecov.io/gh/boostorg/url/pull/602?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 28

> Username: cppalliance-bot  
> Created_at: 2022-10-24 18:53:10 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1289456878  

An automated preview of the documentation is available at [https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://602.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 29

> Username: cppalliance-bot  
> Created_at: 2022-10-24 18:53:54 UTC  
> Url: https://github.com/boostorg/url/pull/602#issuecomment-1289457730  

GCOVR code coverage report [https://602.url.prtest.cppalliance.org/gcovr/index.html](https://602.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://602.url.prtest.cppalliance.org/genhtml/index.html](https://602.url.prtest.cppalliance.org/genhtml/index.html)

---
