# #709 better subview [Closed]

> Username: vinniefalco  
> Created at: 2022-05-26 00:56:14 UTC  
> Updated at: 2022-05-28 17:01:57 UTC  
> Closed at: 2022-05-28 17:01:57 UTC  
> Url: https://github.com/boostorg/json/pull/709  

fix #708

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-26 01:21:29 UTC  
> Updated_at: 2022-05-27 17:16:24 UTC  
> Url: https://github.com/boostorg/json/pull/709#issuecomment-1138040808  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/709?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#709](https://codecov.io/gh/boostorg/json/pull/709?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (43e50e7) into [develop](https://codecov.io/gh/boostorg/json/commit/c4ab4dd2835cd670056934de6f26a6fdf52faeae?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c4ab4dd) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/709/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/709?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #709   +/-   ##  
========================================  
  Coverage    99.08%   99.08%             
========================================  
  Files           69       69             
  Lines         6547     6553    +6       
========================================  
+ Hits          6487     6493    +6       
  Misses          60       60             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/709?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/object.hpp](https://codecov.io/gh/boostorg/json/pull/709/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/string.hpp](https://codecov.io/gh/boostorg/json/pull/709/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/709?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/709?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c4ab4dd...43e50e7](https://codecov.io/gh/boostorg/json/pull/709?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-05-26 02:19:43 UTC  
> Url: https://github.com/boostorg/json/pull/709#issuecomment-1138073601  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/709/pullrequest-condensed-6c6f5e9.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/709/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/709/pullrequest.html)

---

## Comment 3

> Username: Lastique  
> Created_at: 2022-05-26 08:28:37 UTC  
> Url: https://github.com/boostorg/json/pull/709#issuecomment-1138294853  

Comparison operators need to change as well.

---

## Comment 4

> Username: Lastique  
> Created_at: 2022-05-26 08:31:27 UTC  
> Url: https://github.com/boostorg/json/pull/709#issuecomment-1138297162  

Also, although it might fix the Boost.JSON building issue, users' code that invokes conversion to `boost::core::string_view` will still remain affected. I think, the conversion itself needs to be fixed (i.e. one of the two conversion paths should be made less preferable).

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-05-26 13:05:10 UTC  
> Url: https://github.com/boostorg/json/pull/709#issuecomment-1138554780  

Maybe we should apply a gcc 8.3 specific fix?

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-05-26 13:06:37 UTC  
> Url: https://github.com/boostorg/json/pull/709#issuecomment-1138556278  

I updated the comparisons

---

## Review 7 [Commented]

> Username: Lastique  
> Created_at: 2022-05-26 13:13:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/709#pullrequestreview-986192866  

📁 include/boost/json/string.hpp

```diff
2794 | {
2785 |-     return string_view(lhs) == string_view(rhs);
2795 |+     return lhs.subview() == rhs.subview();
```

> Username: Lastique  
> Created_at: 2022-05-26 13:13:05 UTC  
> Updated_at: 2022-05-26 13:13:06 UTC  
> Url: https://github.com/boostorg/json/pull/709#discussion_r882655123  

This may not work if either `T` or `U` is neither `string` or `string_view`. You're testing for `is_convertible`, which allows any type that may not have `subview()` member.

> Username: vinniefalco  
> Created_at: 2022-05-26 13:14:23 UTC  
> Url: https://github.com/boostorg/json/pull/709#discussion_r882656323  

hmm this requires a bit more thought, thanks


---

## Comment 8

> Username: Lastique  
> Created_at: 2022-05-26 13:15:14 UTC  
> Url: https://github.com/boostorg/json/pull/709#issuecomment-1138564710  

> Maybe we should apply a gcc 8.3 specific fix?  
  
I'm not opposed to a gcc-specific workaround. I've just not figured out a way to make the conversions to compile.

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2022-05-26 13:19:14 UTC  
> Url: https://github.com/boostorg/json/pull/709#issuecomment-1138568939  

How about this? https://github.com/boostorg/json/pull/709/commits/1949b0abb0249eec5bd00f3b86e86dd14fedb382

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2022-05-26 13:58:57 UTC  
> Url: https://github.com/boostorg/json/pull/709#issuecomment-1138608770  

It seems to compile on gcc 8.3 but I am not sure whether I need to actually instantiate those operators or not  
https://godbolt.org/z/EdxzEGT4v

---

## Comment 11

> Username: Lastique  
> Created_at: 2022-05-26 14:38:34 UTC  
> Updated_at: 2022-05-26 14:40:07 UTC  
> Url: https://github.com/boostorg/json/pull/709#issuecomment-1138649457  

Seems to be working for my local build of Boost.JSON. Also on [godbolt](https://godbolt.org/z/9crKrMTnG). Thanks!  
  
PS: `mc1` case in the godbolt link above doesn't work, but I don't think it did before. Not sure if this is intended, but it's good enough for me.

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-05-26 14:49:38 UTC  
> Url: https://github.com/boostorg/json/pull/709#issuecomment-1138660326  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/709/pullrequest-condensed-e02a392.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/709/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/709/pullrequest.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-05-26 23:09:37 UTC  
> Url: https://github.com/boostorg/json/pull/709#issuecomment-1139132634  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/709/pullrequest-condensed-7bf2861.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/709/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/709/pullrequest.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2022-05-27 14:19:40 UTC  
> Url: https://github.com/boostorg/json/pull/709#issuecomment-1139664983  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/709/pullrequest-condensed-6b1fe5b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/709/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/709/pullrequest.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2022-05-27 18:14:42 UTC  
> Url: https://github.com/boostorg/json/pull/709#issuecomment-1139909802  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/709/pullrequest-condensed-85910be.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/709/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/709/pullrequest.html)

---
