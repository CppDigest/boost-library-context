# #143 Update internal table to no longer allocate on default constructions and when the bucket count is 0 [Merged]

> Username: cmazakas  
> Created at: 2022-08-24 22:53:58 UTC  
> Updated at: 2022-08-31 16:21:16 UTC  
> Merged at: 2022-08-31 16:21:13 UTC  
> Closed at: 2022-08-31 16:21:13 UTC  
> Url: https://github.com/boostorg/unordered/pull/143  

Also make sure assignment operators don't allocate either as:  
```cpp  
T x;  
T y;  
x = y;  
```  
heavily implies no allocations should be happening.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-08-25 00:39:23 UTC  
> Updated_at: 2022-08-30 22:20:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/143#issuecomment-1226637661  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/143?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#143](https://codecov.io/gh/boostorg/unordered/pull/143?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f03f027) into [develop](https://codecov.io/gh/boostorg/unordered/commit/f141cd1dea6965e36f7415c7d9aec7053a5cdaae?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f141cd1) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head f03f027 differs from pull request most recent head db9d9d1. Consider uploading reports for the commit db9d9d1 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/143/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/143?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop     #143    +/-   ##  
=========================================  
  Coverage    97.40%   97.41%              
=========================================  
  Files           75       75              
  Lines        10190    10313   +123       
=========================================  
+ Hits          9926    10046   +120       
- Misses         264      267     +3       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/143?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/unordered\_map.hpp](https://codecov.io/gh/boostorg/unordered/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX21hcC5ocHA=) | `99.84% <ø> (-0.01%)` | :arrow_down: |  
| [include/boost/unordered/unordered\_set.hpp](https://codecov.io/gh/boostorg/unordered/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX3NldC5ocHA=) | `99.27% <ø> (-0.01%)` | :arrow_down: |  
| [include/boost/unordered/detail/fca.hpp](https://codecov.io/gh/boostorg/unordered/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZjYS5ocHA=) | `99.62% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/unordered/detail/implementation.hpp](https://codecov.io/gh/boostorg/unordered/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ltcGxlbWVudGF0aW9uLmhwcA==) | `98.92% <100.00%> (+<0.01%)` | :arrow_up: |  
| [test/exception/insert\_exception\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9leGNlcHRpb24vaW5zZXJ0X2V4Y2VwdGlvbl90ZXN0cy5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [test/unordered/constructor\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvY29uc3RydWN0b3JfdGVzdHMuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/unordered/move\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvbW92ZV90ZXN0cy5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [test/unordered/rehash\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvcmVoYXNoX3Rlc3RzLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/unordered/scary\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvc2NhcnlfdGVzdHMuY3Bw) | `79.16% <100.00%> (-9.73%)` | :arrow_down: |  
| ... and [3 more](https://codecov.io/gh/boostorg/unordered/pull/143/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/143?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/143?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f141cd1...db9d9d1](https://codecov.io/gh/boostorg/unordered/pull/143?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: joaquintides  
> Created_at: 2022-08-28 17:16:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/143#issuecomment-1229511469  

The following:  
```cpp  
boost::unordered_set<int> s;  
assert(s.bucket_count()==0);  
s.rehash(0);  
std::cout<<s.bucket_count()<<"\n";  
```  
prints 13, bucket count should be kept to 0 in this case, IMHO. Same thing for `reserve(0)`.

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-08-30 19:20:56 UTC  
> Url: https://github.com/boostorg/unordered/pull/143#issuecomment-1232068984  

An automated preview of the documentation is available at [https://143.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://143.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
