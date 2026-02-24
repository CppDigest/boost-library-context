# #747 remove type aliases [Merged]

> Username: alandefreitas  
> Created at: 2023-04-27 22:53:31 UTC  
> Updated at: 2023-07-24 14:37:38 UTC  
> Merged at: 2023-04-28 20:27:26 UTC  
> Closed at: 2023-04-28 20:27:26 UTC  
> Url: https://github.com/boostorg/url/pull/747  

fix #743

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-04-27 23:02:09 UTC  
> Url: https://github.com/boostorg/url/pull/747#issuecomment-1526698494  

An automated preview of the documentation is available at [https://747.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://747.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-04-28 00:47:07 UTC  
> Url: https://github.com/boostorg/url/pull/747#issuecomment-1526824128  

An automated preview of the documentation is available at [https://747.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://747.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2023-04-28 01:07:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/747#pullrequestreview-1405099980  

📁 example/magnet/magnet.cpp

```diff
 356 |     boost::optional<urls::url_view>
 355 |-     url_param(urls::string_view key) const noexcept;
 357 |+     url_param(core::string_view key) const noexcept;
```

> Username: vinniefalco  
> Created_at: 2023-04-28 01:07:33 UTC  
> Updated_at: 2023-04-28 01:07:34 UTC  
> Url: https://github.com/boostorg/url/pull/747#discussion_r1179840935  

Yeah this looks much better.. `core::string_view`... Easier to understand.


---

## Comment 4

> Username: vinniefalco  
> Created_at: 2023-04-28 01:09:14 UTC  
> Url: https://github.com/boostorg/url/pull/747#issuecomment-1526840421  

This is so ugly, I have no words for it:  
  
![image](https://user-images.githubusercontent.com/1503976/235030423-e23db906-11e5-4b1b-81e3-5cea14783127.png)  
  
set them in red ("role=red" or something like that ,look at Beast's reference).  
  
Or put a bullet next to each one and at the top of the page put "bulleted items are deprecated and will be removed in a future release."

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2023-04-28 01:10:42 UTC  
> Url: https://github.com/boostorg/url/pull/747#issuecomment-1526841559  

How do you feel about this change?

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-04-28 01:17:09 UTC  
> Url: https://github.com/boostorg/url/pull/747#issuecomment-1526846010  

An automated preview of the documentation is available at [https://747.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://747.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-04-28 01:27:25 UTC  
> Url: https://github.com/boostorg/url/pull/747#issuecomment-1526851641  

GCOVR code coverage report [https://747.url.prtest.cppalliance.org/gcovr/index.html](https://747.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://747.url.prtest.cppalliance.org/genhtml/index.html](https://747.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://747.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://747.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-04-28 01:37:11 UTC  
> Url: https://github.com/boostorg/url/pull/747#issuecomment-1526857724  

An automated preview of the documentation is available at [https://747.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://747.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-04-28 01:44:56 UTC  
> Url: https://github.com/boostorg/url/pull/747#issuecomment-1526862178  

GCOVR code coverage report [https://747.url.prtest.cppalliance.org/gcovr/index.html](https://747.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://747.url.prtest.cppalliance.org/genhtml/index.html](https://747.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://747.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://747.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-04-28 01:57:07 UTC  
> Url: https://github.com/boostorg/url/pull/747#issuecomment-1526870312  

An automated preview of the documentation is available at [https://747.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://747.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-04-28 02:04:54 UTC  
> Url: https://github.com/boostorg/url/pull/747#issuecomment-1526875584  

GCOVR code coverage report [https://747.url.prtest.cppalliance.org/gcovr/index.html](https://747.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://747.url.prtest.cppalliance.org/genhtml/index.html](https://747.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://747.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://747.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 12

> Username: codecov[bot]  
> Created_at: 2023-04-28 02:10:38 UTC  
> Updated_at: 2023-04-28 19:43:25 UTC  
> Url: https://github.com/boostorg/url/pull/747#issuecomment-1526879742  

## [Codecov](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#747](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a51112d) into [develop](https://codecov.io/gh/boostorg/url/commit/ed7b7c6048b1486f5833b63af9bbf24702a75704?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ed7b7c6) will **not change** coverage.  
> The diff coverage is `98.60%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/747/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #747   +/-   ##  
========================================  
  Coverage    97.25%   97.25%             
========================================  
  Files          155      155             
  Lines         8517     8517             
========================================  
  Hits          8283     8283             
  Misses         234      234             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [example/router/detail/router.hpp](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvZGV0YWlsL3JvdXRlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [example/router/impl/matches.cpp](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvaW1wbC9tYXRjaGVzLmNwcA==) | `100.00% <ø> (ø)` | |  
| [example/router/matches.hpp](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvbWF0Y2hlcy5ocHA=) | `100.00% <ø> (ø)` | |  
| [example/router/router.hpp](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvcm91dGVyLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/decode\_view.hpp](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGVjb2RlX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/any\_params\_iter.hpp](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9wYXJhbXNfaXRlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/any\_segments\_iter.hpp](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9zZWdtZW50c19pdGVyLmhwcA==) | `98.50% <ø> (ø)` | |  
| [include/boost/url/detail/encode.hpp](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2VuY29kZS5ocHA=) | `63.63% <ø> (ø)` | |  
| [include/boost/url/detail/except.hpp](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2V4Y2VwdC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/path.hpp](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3BhdGguaHBw) | `100.00% <ø> (ø)` | |  
| ... and [120 more](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ed7b7c6...a51112d](https://codecov.io/gh/boostorg/url/pull/747?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-04-28 18:52:15 UTC  
> Url: https://github.com/boostorg/url/pull/747#issuecomment-1527961866  

An automated preview of the documentation is available at [https://747.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://747.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2023-04-28 19:02:40 UTC  
> Url: https://github.com/boostorg/url/pull/747#issuecomment-1527973462  

GCOVR code coverage report [https://747.url.prtest.cppalliance.org/gcovr/index.html](https://747.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://747.url.prtest.cppalliance.org/genhtml/index.html](https://747.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://747.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://747.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2023-04-28 19:27:11 UTC  
> Url: https://github.com/boostorg/url/pull/747#issuecomment-1527997394  

An automated preview of the documentation is available at [https://747.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://747.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2023-04-28 19:33:01 UTC  
> Url: https://github.com/boostorg/url/pull/747#issuecomment-1528002278  

GCOVR code coverage report [https://747.url.prtest.cppalliance.org/gcovr/index.html](https://747.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://747.url.prtest.cppalliance.org/genhtml/index.html](https://747.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://747.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://747.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
