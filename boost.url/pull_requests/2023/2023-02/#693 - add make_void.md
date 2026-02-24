# #693 add make_void [Merged]

> Username: alandefreitas  
> Created at: 2023-02-22 19:55:10 UTC  
> Updated at: 2024-02-21 00:54:44 UTC  
> Merged at: 2023-03-01 21:44:50 UTC  
> Closed at: 2023-03-01 21:44:50 UTC  
> Url: https://github.com/boostorg/url/pull/693  

fix #688

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2023-02-22 19:57:03 UTC  
> Url: https://github.com/boostorg/url/pull/693#issuecomment-1440714724  

1. put it in the main namespace not detail, and  
2. is this `pos_t` a stray unused type?  
https://github.com/boostorg/url/blob/8e2693a838be733f72dba366b3165cf36be5ff4c/include/boost/url/detail/config.hpp#L93  
  
maybe we should remove `pos_t`?

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-02-22 20:10:17 UTC  
> Url: https://github.com/boostorg/url/pull/693#issuecomment-1440735081  

GCOVR code coverage report [https://693.url.prtest.cppalliance.org/gcovr/index.html](https://693.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://693.url.prtest.cppalliance.org/genhtml/index.html](https://693.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://693.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://693.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-02-22 20:14:51 UTC  
> Updated_at: 2023-03-01 21:28:08 UTC  
> Url: https://github.com/boostorg/url/pull/693#issuecomment-1440740371  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#693](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0642f6a) into [develop](https://codecov.io/gh/boostorg/url/commit/229c925040c99cd49cb5ab416e8772a9093f953f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (229c925) will **decrease** coverage by `0.01%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/693/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #693      +/-   ##  
===========================================  
- Coverage    97.13%   97.13%   -0.01%       
===========================================  
  Files          152      154       +2       
  Lines         8445     8433      -12       
===========================================  
- Hits          8203     8191      -12       
  Misses         242      242                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/optional\_string.hpp](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL29wdGlvbmFsX3N0cmluZy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/url\_impl.hpp](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3VybF9pbXBsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/charset.hpp](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9jaGFyc2V0LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/detail/charset.hpp](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9kZXRhaWwvY2hhcnNldC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/detail/ci\_string.hpp](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9kZXRhaWwvY2lfc3RyaW5nLmhwcA==) | `96.00% <ø> (ø)` | |  
| [include/boost/url/grammar/lut\_chars.hpp](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9sdXRfY2hhcnMuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/string\_token.hpp](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9zdHJpbmdfdG9rZW4uaHBw) | `97.67% <ø> (ø)` | |  
| [include/boost/url/impl/error.ipp](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9lcnJvci5pcHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/grammar/impl/error.ipp](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL2Vycm9yLmlwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/grammar/impl/error.hpp](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL2Vycm9yLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| ... and [1 more](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [229c925...0642f6a](https://codecov.io/gh/boostorg/url/pull/693?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2023-02-22 20:19:18 UTC  
> Url: https://github.com/boostorg/url/pull/693#issuecomment-1440745240  

> maybe we should remove pos_t?  
  
In another PR, right?

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2023-02-22 20:20:24 UTC  
> Url: https://github.com/boostorg/url/pull/693#issuecomment-1440746373  

> put it in the main namespace not detail  
  
I was a little confused by that too. boost.buffers put it in main namespace but the issue said `detail::` so I gave preference to the issue.  
  
I'll change it.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2023-02-22 20:28:43 UTC  
> Url: https://github.com/boostorg/url/pull/693#issuecomment-1440755453  

> In another PR, right?  
  
That's up to you

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-02-22 20:59:37 UTC  
> Url: https://github.com/boostorg/url/pull/693#issuecomment-1440789159  

GCOVR code coverage report [https://693.url.prtest.cppalliance.org/gcovr/index.html](https://693.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://693.url.prtest.cppalliance.org/genhtml/index.html](https://693.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://693.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://693.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-02-22 21:10:47 UTC  
> Url: https://github.com/boostorg/url/pull/693#issuecomment-1440801200  

GCOVR code coverage report [https://693.url.prtest.cppalliance.org/gcovr/index.html](https://693.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://693.url.prtest.cppalliance.org/genhtml/index.html](https://693.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://693.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://693.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-03-01 21:25:41 UTC  
> Url: https://github.com/boostorg/url/pull/693#issuecomment-1450868503  

GCOVR code coverage report [https://693.url.prtest.cppalliance.org/gcovr/index.html](https://693.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://693.url.prtest.cppalliance.org/genhtml/index.html](https://693.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://693.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://693.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
