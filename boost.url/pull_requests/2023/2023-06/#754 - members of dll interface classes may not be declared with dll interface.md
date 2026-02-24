# #754 members of dll interface classes may not be declared with dll interface [Merged]

> Username: alandefreitas  
> Created at: 2023-06-09 09:57:51 UTC  
> Updated at: 2023-07-24 14:37:57 UTC  
> Merged at: 2023-06-10 00:12:59 UTC  
> Closed at: 2023-06-10 00:12:59 UTC  
> Url: https://github.com/boostorg/url/pull/754  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-06-09 10:17:45 UTC  
> Url: https://github.com/boostorg/url/pull/754#issuecomment-1584336497  

GCOVR code coverage report [https://754.url.prtest.cppalliance.org/gcovr/index.html](https://754.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://754.url.prtest.cppalliance.org/genhtml/index.html](https://754.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2023-06-09 11:58:38 UTC  
> Url: https://github.com/boostorg/url/pull/754#issuecomment-1584462513  

What the heck is going on here? You mean we dont need to put _DECL in front of everything? What does this mean for MrDox and all the other libs?

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2023-06-09 17:28:04 UTC  
> Url: https://github.com/boostorg/url/pull/754#issuecomment-1584917915  

> What the heck is going on here?  
  
Now that the matrix is more complete and I see errors from other compilers, I noticed a lot of what we had was simply wrong. Many classes didn't even have BOOST_URL_DECL and the tests weren't catching it. In a way, mingw was very helpful because it's very strict about symbols.  
  
I had a look at the boost.config documentation again, replicated patterns from other libraries, and kept fixing everything that one of the compilers considered an error until I got to this implementation.  
  
> You mean we dont need to put _DECL in front of everything?   
  
Yes. "members of dll interface class may not be declared with dll interface" is literally a compile error message.  
  
> What does this mean for MrDox and all the other libs?  
  
I don't know if there's a specific recommendation. I just kept testing it and fixing what was wrong according to one of the compilers. We just have to keep doing it for other libraries.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-06-09 18:43:45 UTC  
> Url: https://github.com/boostorg/url/pull/754#issuecomment-1584997517  

GCOVR code coverage report [https://754.url.prtest.cppalliance.org/gcovr/index.html](https://754.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://754.url.prtest.cppalliance.org/genhtml/index.html](https://754.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-06-09 18:59:44 UTC  
> Url: https://github.com/boostorg/url/pull/754#issuecomment-1585012957  

GCOVR code coverage report [https://754.url.prtest.cppalliance.org/gcovr/index.html](https://754.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://754.url.prtest.cppalliance.org/genhtml/index.html](https://754.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-06-09 19:44:28 UTC  
> Url: https://github.com/boostorg/url/pull/754#issuecomment-1585053786  

GCOVR code coverage report [https://754.url.prtest.cppalliance.org/gcovr/index.html](https://754.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://754.url.prtest.cppalliance.org/genhtml/index.html](https://754.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-06-09 20:14:06 UTC  
> Url: https://github.com/boostorg/url/pull/754#issuecomment-1585080405  

GCOVR code coverage report [https://754.url.prtest.cppalliance.org/gcovr/index.html](https://754.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://754.url.prtest.cppalliance.org/genhtml/index.html](https://754.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-06-09 20:42:42 UTC  
> Url: https://github.com/boostorg/url/pull/754#issuecomment-1585105326  

GCOVR code coverage report [https://754.url.prtest.cppalliance.org/gcovr/index.html](https://754.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://754.url.prtest.cppalliance.org/genhtml/index.html](https://754.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2023-06-09 20:43:51 UTC  
> Url: https://github.com/boostorg/url/pull/754#issuecomment-1585106138  

TIL

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-06-09 21:38:19 UTC  
> Url: https://github.com/boostorg/url/pull/754#issuecomment-1585152170  

GCOVR code coverage report [https://754.url.prtest.cppalliance.org/gcovr/index.html](https://754.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://754.url.prtest.cppalliance.org/genhtml/index.html](https://754.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-06-09 23:12:08 UTC  
> Url: https://github.com/boostorg/url/pull/754#issuecomment-1585229625  

An automated preview of the documentation is available at [https://754.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://754.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-06-09 23:23:20 UTC  
> Url: https://github.com/boostorg/url/pull/754#issuecomment-1585236231  

GCOVR code coverage report [https://754.url.prtest.cppalliance.org/gcovr/index.html](https://754.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://754.url.prtest.cppalliance.org/genhtml/index.html](https://754.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://754.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 13

> Username: codecov[bot]  
> Created_at: 2023-06-09 23:25:11 UTC  
> Updated_at: 2023-06-09 23:25:31 UTC  
> Url: https://github.com/boostorg/url/pull/754#issuecomment-1585239847  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#754](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (846db5f) into [develop](https://app.codecov.io/gh/boostorg/url/commit/381033202c7d510292eb70a5f36e374b53b278e9?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3810332) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/754/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #754   +/-   ##  
========================================  
  Coverage    97.25%   97.25%             
========================================  
  Files          155      155             
  Lines         8517     8518    +1       
========================================  
+ Hits          8283     8284    +1       
  Misses         234      234             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [example/router/detail/impl/router.cpp](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvZGV0YWlsL2ltcGwvcm91dGVyLmNwcA==) | `100.00% <ø> (ø)` | |  
| [example/router/detail/router.hpp](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvZGV0YWlsL3JvdXRlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/authority\_view.hpp](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `93.75% <ø> (ø)` | |  
| [include/boost/url/detail/any\_params\_iter.hpp](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9wYXJhbXNfaXRlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/params\_iter\_impl.hpp](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3BhcmFtc19pdGVyX2ltcGwuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/url\_impl.hpp](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3VybF9pbXBsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/impl/error.hpp](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL2Vycm9yLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/error.hpp](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9lcnJvci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/params\_base.hpp](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfYmFzZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/params\_encoded\_ref.hpp](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZF9yZWYuaHBw) | `100.00% <ø> (ø)` | |  
| ... and [13 more](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/url/pull/754/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3810332...846db5f](https://app.codecov.io/gh/boostorg/url/pull/754?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
