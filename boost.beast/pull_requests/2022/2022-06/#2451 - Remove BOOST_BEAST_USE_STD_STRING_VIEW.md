# #2451 Remove BOOST_BEAST_USE_STD_STRING_VIEW [Merged]

> Username: sehe  
> Created at: 2022-06-15 14:15:58 UTC  
> Updated at: 2022-10-03 05:29:21 UTC  
> Merged at: 2022-10-03 05:29:21 UTC  
> Closed at: 2022-10-03 05:29:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2451  

Fixes #2363

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-06-15 14:21:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2451#issuecomment-1156538752  

An automated preview of the documentation is available at [https://2451.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2451.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-06-15 14:43:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2451#issuecomment-1156564708  

![pullrequest](https://2451.beast.tracing.cppalliance.org/pullrequest-fc2e1aad.png)  
Timeline tracing charts: [https://2451.beast.tracing.cppalliance.org/index.html](https://2451.beast.tracing.cppalliance.org/index.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-06-21 23:34:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2451#issuecomment-1162462088  

We can't just remove it, we have to deprecate it first for at least one release

---

## Comment 4

> Username: sehe  
> Created_at: 2022-06-22 14:06:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2451#issuecomment-1163145801  

Does that include a preprocessor warning (well, error, for portability) when it's used? Or just a deprecation notice in the release notes

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-06-22 17:24:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2451#issuecomment-1163410157  

it should be an error or warning unless they define `BOOST_BEAST_ALLOW_DEPRECATED` or something like that.

---

## Review 6 [Commented]

> Username: klemens-morgenstern  
> Created_at: 2022-10-02 13:39:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2451#pullrequestreview-1127646399  

📁 include/boost/beast/core/string_type.hpp

```diff
  16 |- #include <string_view>
  17 |- #else
  15 | #include <boost/core/detail/string_view.hpp>
```

> Username: klemens-morgenstern  
> Created_at: 2022-10-02 13:39:32 UTC  
> Updated_at: 2022-10-02 13:40:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2451#discussion_r985241355  

```diff  
+#include <boost/core/detail/string_view.hpp>  
+  
+#if defined(BOOST_BEAST_USE_STD_STRING_VIEW)  
+BOOST_PRAGMA_MESSAGE("BOOST_BEAST_USE_STD_STRING_VIEW is deprecated, use BOOST_NO_CXX17_HDR_STRING_VIEW instead")  
+#endif  
```


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-10-02 13:46:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2451#issuecomment-1264648306  

An automated preview of the documentation is available at [https://2451.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2451.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-10-02 14:05:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2451#issuecomment-1264651973  

![pullrequest](https://2451.beast.tracing.cppalliance.org/pullrequest-fb6b7bca.png)  
Timeline tracing charts: [https://2451.beast.tracing.cppalliance.org/index.html](https://2451.beast.tracing.cppalliance.org/index.html)

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2022-10-02 19:29:16 UTC  
> Url: https://github.com/boostorg/beast/pull/2451#issuecomment-1264715746  

This needs to be squashed, with authorship preserved, and the commit message edited to reflect the semantics of the change, i.e. "std::string_view is not an option"

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-10-03 02:26:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2451#issuecomment-1264849117  

An automated preview of the documentation is available at [https://2451.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2451.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-10-03 02:42:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2451#issuecomment-1264859736  

![pullrequest](https://2451.beast.tracing.cppalliance.org/pullrequest-e0647555.png)  
Timeline tracing charts: [https://2451.beast.tracing.cppalliance.org/index.html](https://2451.beast.tracing.cppalliance.org/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-10-03 03:26:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2451#issuecomment-1264884585  

An automated preview of the documentation is available at [https://2451.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2451.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-10-03 03:41:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2451#issuecomment-1264891979  

![pullrequest](https://2451.beast.tracing.cppalliance.org/pullrequest-19e054ce.png)  
Timeline tracing charts: [https://2451.beast.tracing.cppalliance.org/index.html](https://2451.beast.tracing.cppalliance.org/index.html)

---

## Comment 14

> Username: codecov[bot]  
> Created_at: 2022-10-03 04:08:45 UTC  
> Updated_at: 2022-10-03 04:18:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2451#issuecomment-1264903863  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2451?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2451](https://codecov.io/gh/boostorg/beast/pull/2451?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6869502) into [develop](https://codecov.io/gh/boostorg/beast/commit/12c2d14ebc684700c75d11b2b626021682fdb2d1?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (12c2d14) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2451/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2451?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2451   +/-   ##  
========================================  
  Coverage    94.72%   94.72%             
========================================  
  Files          152      152             
  Lines        11881    11881             
========================================  
  Hits         11254    11254             
  Misses         627      627             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2451?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/string\_type.hpp](https://codecov.io/gh/boostorg/beast/pull/2451/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0cmluZ190eXBlLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2451?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2451?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [12c2d14...6869502](https://codecov.io/gh/boostorg/beast/pull/2451?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
