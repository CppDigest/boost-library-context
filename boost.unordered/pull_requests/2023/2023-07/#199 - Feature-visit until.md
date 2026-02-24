# #199 Feature/visit until [Merged]

> Username: joaquintides  
> Created at: 2023-07-24 16:50:23 UTC  
> Updated at: 2023-08-11 19:26:21 UTC  
> Merged at: 2023-08-11 19:26:13 UTC  
> Closed at: 2023-08-11 19:26:13 UTC  
> Url: https://github.com/boostorg/unordered/pull/199  

Tests pending from @cmazakas, PR open for discussion. I think the API is quite uncontroversial, note however `[c]visit_(until|while)` returns a `bool`,  whereas `[c]visit_all` returns `size_type` (non-parallel versions) or `void` (parallel versions).

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-07-24 17:02:13 UTC  
> Url: https://github.com/boostorg/unordered/pull/199#issuecomment-1648278997  

An automated preview of the documentation is available at [https://199.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://199.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 2

> Username: pdimov  
> Created_at: 2023-07-24 17:48:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/199#issuecomment-1648344702  

Why do we need both _while and _until?

---

## Comment 3

> Username: joaquintides  
> Created_at: 2023-07-24 17:53:54 UTC  
> Updated_at: 2023-07-24 17:54:43 UTC  
> Url: https://github.com/boostorg/unordered/pull/199#issuecomment-1648351320  

We don't need both more than the standard library needs all of  `std::all_of`, `std::any_of` and `std::none_of`.  Besides, I couln't decide which is more "natural" (until, maybe).

---

## Comment 4

> Username: pdimov  
> Created_at: 2023-07-24 17:57:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/199#issuecomment-1648356018  

I don't think the duplication helps anyone; it just makes one wonder what's the subtle difference between the two (hence my question - I wasn't sure whether they are duplicates or not).  
  
_while is more natural in C++ because there's no `until` keyword.

---

## Comment 5

> Username: joaquintides  
> Created_at: 2023-07-24 17:59:27 UTC  
> Url: https://github.com/boostorg/unordered/pull/199#issuecomment-1648358954  

Ok, I can drop `_until`.

---

## Comment 6

> Username: joaquintides  
> Created_at: 2023-07-24 18:17:50 UTC  
> Url: https://github.com/boostorg/unordered/pull/199#issuecomment-1648382711  

Dropped. Note that the remaining example:  
```cpp  
// finds the key to a given (unique) value  
  
int  key = 0;  
int  value = ...;  
bool found = !m.visit_while([&](const auto& x) {  
  if(x.second == value) {  
    key = x.first;  
    return false; // finish  
  }  
  else {  
    return true;  // keep on visiting  
  }  
});  
  
if(found) { ... }  
```  
  
is more contrived with `_while`, but I felt looking for a specific value is more interesting than the other, deleted snippet:  
  
```cpp  
// check if all values are != 0  
bool all_ok = m.visit_while([&](const auto& x) {  
  return x.second != 0;  
});  
```  
  
If you guys think otherwise I can change this bit, of course.

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-07-24 18:22:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/199#issuecomment-1648388266  

An automated preview of the documentation is available at [https://199.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://199.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-07-24 18:30:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/199#issuecomment-1648398419  

An automated preview of the documentation is available at [https://199.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://199.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 9

> Username: codecov[bot]  
> Created_at: 2023-07-25 14:09:01 UTC  
> Updated_at: 2023-07-27 06:02:43 UTC  
> Url: https://github.com/boostorg/unordered/pull/199#issuecomment-1649918845  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/199?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#199](https://app.codecov.io/gh/boostorg/unordered/pull/199?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a22c133) into [develop](https://app.codecov.io/gh/boostorg/unordered/commit/bd24dfd284dbc70e7521915af0d8d049f74a1e85?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (bd24dfd) will **increase** coverage by `0.02%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/199/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #199      +/-   ##  
===========================================  
+ Coverage    97.87%   97.89%   +0.02%       
===========================================  
  Files          128      128                
  Lines        18642    18824     +182       
===========================================  
+ Hits         18245    18427     +182       
  Misses         397      397                
```  
  
  
| [Files Changed](https://app.codecov.io/gh/boostorg/unordered/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/concurrent\_flat\_map.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvY29uY3VycmVudF9mbGF0X21hcC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [...de/boost/unordered/detail/foa/concurrent\_table.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb25jdXJyZW50X3RhYmxlLmhwcA==) | `99.58% <100.00%> (+0.05%)` | :arrow_up: |  
| [test/cfoa/visit\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL3Zpc2l0X3Rlc3RzLmNwcA==) | `99.52% <100.00%> (+0.17%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/199?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/199?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [bd24dfd...a22c133](https://app.codecov.io/gh/boostorg/unordered/pull/199?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-07-26 17:22:11 UTC  
> Url: https://github.com/boostorg/unordered/pull/199#issuecomment-1652216445  

An automated preview of the documentation is available at [https://199.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://199.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
