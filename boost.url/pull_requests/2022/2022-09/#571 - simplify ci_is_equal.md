# #571 simplify ci_is_equal [Closed]

> Username: alandefreitas  
> Created at: 2022-09-27 20:41:23 UTC  
> Updated at: 2022-10-09 20:19:15 UTC  
> Closed at: 2022-09-27 21:24:55 UTC  
> Url: https://github.com/boostorg/url/pull/571  



---

## Comment 1

> Username: alandefreitas  
> Created_at: 2022-09-27 20:42:32 UTC  
> Url: https://github.com/boostorg/url/pull/571#issuecomment-1260026194  

This function was duplicated.  
  
I was looking for bad pointer operations #467 but I couldn't find any.   
  
But I ended up simplifying this while doing it.

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-09-27 20:53:33 UTC  
> Url: https://github.com/boostorg/url/pull/571#issuecomment-1260041302  

GCOVR code coverage report [https://571.url.prtest.cppalliance.org/gcovr/index.html](https://571.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://571.url.prtest.cppalliance.org/genhtml/index.html](https://571.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-09-27 20:57:13 UTC  
> Updated_at: 2022-09-27 21:00:25 UTC  
> Url: https://github.com/boostorg/url/pull/571#issuecomment-1260045161  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/571?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#571](https://codecov.io/gh/boostorg/url/pull/571?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2c046e4) into [develop](https://codecov.io/gh/boostorg/url/commit/81cb06ec5e616d609ac01ef1b6a5d67eaa8448e2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (81cb06e) will **decrease** coverage by `0.25%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/571/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/571?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #571      +/-   ##  
===========================================  
- Coverage    96.76%   96.50%   -0.26%       
===========================================  
  Files          138      138                
  Lines         6700     6704       +4       
===========================================  
- Hits          6483     6470      -13       
- Misses         217      234      +17       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/571?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/grammar/detail/ci\_string.hpp](https://codecov.io/gh/boostorg/url/pull/571/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9kZXRhaWwvY2lfc3RyaW5nLmhwcA==) | `100.00% <100.00%> (+4.00%)` | :arrow_up: |  
| [include/boost/url/grammar/impl/ci\_string.ipp](https://codecov.io/gh/boostorg/url/pull/571/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL2NpX3N0cmluZy5pcHA=) | `67.24% <0.00%> (-31.04%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/571?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/571?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [81cb06e...2c046e4](https://codecov.io/gh/boostorg/url/pull/571?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-27 21:09:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/571#pullrequestreview-1122700095  

📁 include/boost/url/grammar/detail/impl/ci_string.ipp

```diff
  53 |-     return true;
  54 |- }
  25 |+ extern template bool ci_is_equal<string_view, string_view>(string_view const&, string_view const&);
```

> Username: vinniefalco  
> Created_at: 2022-09-27 21:09:10 UTC  
> Updated_at: 2022-09-27 21:09:11 UTC  
> Url: https://github.com/boostorg/url/pull/571#discussion_r981717200  

Why is this line so long


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-27 21:10:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/571#pullrequestreview-1122701905  

📁 include/boost/url/grammar/detail/ci_string.hpp

```diff
 137 |+     auto n = s0.size();
 138 |+     auto p1 = s0.begin();
 139 |+     auto p2 = s1.begin();
```

> Username: vinniefalco  
> Created_at: 2022-09-27 21:10:59 UTC  
> Url: https://github.com/boostorg/url/pull/571#discussion_r981718506  

p0 and p1... why did you renumber them


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-27 21:11:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/571#pullrequestreview-1122702061  

📁 include/boost/url/grammar/detail/ci_string.hpp

```diff
 138 |+     auto p1 = s0.begin();
 139 |+     auto p2 = s1.begin();
 140 |+     char a, b;
```

> Username: vinniefalco  
> Created_at: 2022-09-27 21:11:09 UTC  
> Url: https://github.com/boostorg/url/pull/571#discussion_r981718628  

c0, c1? heh


---

## Comment 7

> Username: vinniefalco  
> Created_at: 2022-10-07 21:35:18 UTC  
> Url: https://github.com/boostorg/url/pull/571#issuecomment-1272104714  

Was this merged?

---

## Comment 8

> Username: alandefreitas  
> Created_at: 2022-10-07 22:24:01 UTC  
> Url: https://github.com/boostorg/url/pull/571#issuecomment-1272131481  

> Was this merged?  
  
No. You told me to give up on this.

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2022-10-08 00:08:10 UTC  
> Url: https://github.com/boostorg/url/pull/571#issuecomment-1272170892  

The change to `all_chars_t` is good, we should keep that

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2022-10-08 00:09:14 UTC  
> Url: https://github.com/boostorg/url/pull/571#issuecomment-1272171212  

Was this the thing with the problem of not having `size()` on a range?

---

## Comment 11

> Username: alandefreitas  
> Created_at: 2022-10-09 20:19:15 UTC  
> Url: https://github.com/boostorg/url/pull/571#issuecomment-1272620387  

> Was this the thing with the problem of not having size() on a range?  
  
Yes. This is related to the problem here. And that's why you told me to keep both functions.

---
