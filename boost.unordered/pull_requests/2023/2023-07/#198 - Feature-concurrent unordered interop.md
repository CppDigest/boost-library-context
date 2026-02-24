# #198 Feature/concurrent unordered interop [Merged]

> Username: joaquintides  
> Created at: 2023-07-23 10:24:28 UTC  
> Updated at: 2023-08-12 21:07:54 UTC  
> Merged at: 2023-08-12 07:48:08 UTC  
> Closed at: 2023-08-12 07:48:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/198  

PR in progress (testing not done yet, for instance), I'm opening it in draft mode for discussion

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-07-23 10:32:03 UTC  
> Url: https://github.com/boostorg/unordered/pull/198#issuecomment-1646804617  

An automated preview of the documentation is available at [https://198.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://198.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-07-23 10:52:12 UTC  
> Updated_at: 2023-07-26 12:28:14 UTC  
> Url: https://github.com/boostorg/unordered/pull/198#issuecomment-1646808231  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/198?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#198](https://app.codecov.io/gh/boostorg/unordered/pull/198?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e9aa3d7) into [develop](https://app.codecov.io/gh/boostorg/unordered/commit/bd24dfd284dbc70e7521915af0d8d049f74a1e85?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (bd24dfd) will **increase** coverage by `0.02%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/198/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/198?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #198      +/-   ##  
===========================================  
+ Coverage    97.87%   97.89%   +0.02%       
===========================================  
  Files          128      128                
  Lines        18642    18819     +177       
===========================================  
+ Hits         18245    18422     +177       
  Misses         397      397                
```  
  
  
| [Files Changed](https://app.codecov.io/gh/boostorg/unordered/pull/198?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/concurrent\_flat\_map.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/198?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvY29uY3VycmVudF9mbGF0X21hcC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [...de/boost/unordered/detail/foa/concurrent\_table.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/198?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb25jdXJyZW50X3RhYmxlLmhwcA==) | `99.54% <100.00%> (+0.01%)` | :arrow_up: |  
| [include/boost/unordered/detail/foa/core.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/198?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb3JlLmhwcA==) | `99.82% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/unordered/detail/foa/table.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/198?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS90YWJsZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/unordered/unordered\_flat\_map.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/198?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX2ZsYXRfbWFwLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/cfoa/assign\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/198?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL2Fzc2lnbl90ZXN0cy5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [test/cfoa/constructor\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/198?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL2NvbnN0cnVjdG9yX3Rlc3RzLmNwcA==) | `98.52% <100.00%> (+0.19%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/198?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/198?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [bd24dfd...e9aa3d7](https://app.codecov.io/gh/boostorg/unordered/pull/198?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-07-23 10:57:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/198#issuecomment-1646809194  

Looks OK to me so far, but needs tests.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-07-23 12:47:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/198#issuecomment-1646832139  

An automated preview of the documentation is available at [https://198.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://198.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 5

> Username: joaquintides  
> Created_at: 2023-07-23 15:00:28 UTC  
> Updated_at: 2023-07-23 15:01:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/198#issuecomment-1646863311  

> Looks OK to me so far, but needs tests.  
  
Yes, I'd like to ask @cmazakas to fill that in. A couple of points:  
  
- We don't need specialized assignment operators: `unordered_m=std::move(concurrent_m)` does the right thing via an intermediate `boost::unordered_flat_map` move construction (same for the opposite direction).  
- We discussed this some weeks ago: the unordered-->concurrent direction incurs an allocation for the access structure and is technically O(n), though expected to be very fast (zero initialization of spinlocks and insertion counters). My opinion is that this is OK as is: I don't think swtching between one type of container and the other is something the program will do many times in a hot loop or something. But I'd like to know your opinion, of course.

---

## Comment 6

> Username: pdimov  
> Created_at: 2023-07-23 16:57:43 UTC  
> Url: https://github.com/boostorg/unordered/pull/198#issuecomment-1646890512  

Speed is not the problem - the possibility to throw is. But since this is a heterogeneous move constructor, it should be fine.

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-07-24 20:22:34 UTC  
> Url: https://github.com/boostorg/unordered/pull/198#issuecomment-1648550991  

An automated preview of the documentation is available at [https://198.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://198.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 8

> Username: cmazakas  
> Created_at: 2023-07-24 22:29:24 UTC  
> Url: https://github.com/boostorg/unordered/pull/198#issuecomment-1648711326  

Everything looks good to me as well. Took me awhile to finally understand everything.  
  
I've added some tests that test construction from unordered->concurrent and then vice versa.  
  
This is an allocating move constructor, sure, but I don't think it'll be a problem in practice. I think on some level it's to be expected by users but only time will tell.

---

## Comment 9

> Username: pdimov  
> Created_at: 2023-07-24 23:29:58 UTC  
> Url: https://github.com/boostorg/unordered/pull/198#issuecomment-1648757130  

You should add move assignment tests as well.

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-07-25 19:17:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/198#issuecomment-1650398280  

An automated preview of the documentation is available at [https://198.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://198.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-08-11 19:57:07 UTC  
> Url: https://github.com/boostorg/unordered/pull/198#issuecomment-1675298089  

An automated preview of the documentation is available at [https://198.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://198.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
