# #2418 Issue #2417 use boost::core::string_view [Merged]

> Username: sehe  
> Created at: 2022-05-02 05:04:20 UTC  
> Updated at: 2022-06-08 23:30:35 UTC  
> Merged at: 2022-05-10 00:47:28 UTC  
> Closed at: 2022-05-10 00:47:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2418  

This improves inter-conversion between string_view implementations. Some observable differences for users:  
 - core::string_view no longer supports the .to_string() or .clear() extensions from Utility  
 - code that relied on .max_size() returning .size(), needs to be fixed to .size() instead  
 - remove_suffix() and remove_prefix() were more lenient than the standard specs; be sure you don't rely on it clamping the argument to valid range  
 - BOOST_NO_CXX11_EXPLICIT_CONVERSION_OPERATORS no longer suppresses conversions to std::string  
 - core::string_view adds .contains() and various bugs fixed  
      
This also revealed several bugs against Boost Utility, and two feature requests for Boost Core

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-05-02 05:11:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1114502813  

An automated preview of the documentation is available at [https://2418.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2418.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-05-02 05:33:16 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1114512445  

![pullrequest](https://2418.beast.tracing.cppalliance.org/pullrequest-8b359bc8.png)  
Timeline tracing charts: [https://2418.beast.tracing.cppalliance.org/index.html](https://2418.beast.tracing.cppalliance.org/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-05-02 06:01:52 UTC  
> Updated_at: 2022-05-09 01:16:28 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1114523235  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2418?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2418](https://codecov.io/gh/boostorg/beast/pull/2418?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2d7b5b5) into [develop](https://codecov.io/gh/boostorg/beast/commit/90e37ae40bc80c99d1fae6078a6843a7e1d8f067?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (90e37ae) will **increase** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2418/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2418?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2418   +/-   ##  
========================================  
  Coverage    94.66%   94.67%             
========================================  
  Files          149      149             
  Lines        11773    11773             
========================================  
+ Hits         11145    11146    +1       
+ Misses         628      627    -1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2418?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/string\_type.hpp](https://codecov.io/gh/boostorg/beast/pull/2418/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0cmluZ190eXBlLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2418/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `95.61% <0.00%> (+0.87%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2418?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2418?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [90e37ae...2d7b5b5](https://codecov.io/gh/boostorg/beast/pull/2418?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-05-02 20:04:51 UTC  
> Updated_at: 2022-05-02 20:10:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1115309250  

Mmm... So the observable differences for users had no impact on the current implementation, right?  
  
I just had a look at occurrences of `max_size()`, `remove_suffix()`, and `remove_prefix()`, and there are no differences that would impact beast.

---

## Comment 5

> Username: sehe  
> Created_at: 2022-05-02 21:45:23 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1115401159  

@alandefreitas Correct, which is why I went ahead and created the PR and suggested release notes to detail any _external_ observabilities that users might encounter in their own code using Beast.

---

## Comment 6

> Username: sehe  
> Created_at: 2022-05-08 15:57:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1120443406  

@vinniefalco marked as ready for review since the hash_value support is in Core. If you think we should go the deprecation warning/conditional compilation route instead, let me know.

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-05-08 16:00:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1120443829  

An automated preview of the documentation is available at [https://2418.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2418.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2022-05-08 16:14:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1120446105  

I would just change the commit message to  
```  
Use core string_view:  
  
fix #2417  
  
This improves inter-conversion...  
```

---

## Comment 9

> Username: sehe  
> Created_at: 2022-05-08 16:17:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1120446567  

Also, turns out I was just copy pasting the bullet list formatting from a place in the release-notes where it was /also/ not rendering correctly. :derp: Hopefully now it comes out readable.

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2022-05-08 16:22:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1120447326  

Yep that's why we have doc previews :) (Thanks Sam!)  
https://2418.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/beast/release_notes.html

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-05-08 16:23:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1120447481  

![pullrequest](https://2418.beast.tracing.cppalliance.org/pullrequest-d22a049d.png)  
Timeline tracing charts: [https://2418.beast.tracing.cppalliance.org/index.html](https://2418.beast.tracing.cppalliance.org/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-05-08 16:24:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1120447606  

An automated preview of the documentation is available at [https://2418.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2418.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-05-08 16:41:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1120450135  

![pullrequest](https://2418.beast.tracing.cppalliance.org/pullrequest-57b41ea1.png)  
Timeline tracing charts: [https://2418.beast.tracing.cppalliance.org/index.html](https://2418.beast.tracing.cppalliance.org/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2022-05-09 00:14:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1120515657  

An automated preview of the documentation is available at [https://2418.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2418.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 15

> Username: sehe  
> Created_at: 2022-05-09 00:29:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1120519175  

Finally fixed the release notes. Took the liberty to fix some of the prior broken lists as well. It took me setting up local automatic doc generation, but now we have something :)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2022-05-09 00:33:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1120520302  

![pullrequest](https://2418.beast.tracing.cppalliance.org/pullrequest-268be355.png)  
Timeline tracing charts: [https://2418.beast.tracing.cppalliance.org/index.html](https://2418.beast.tracing.cppalliance.org/index.html)

---

## Comment 17

> Username: vinniefalco  
> Created_at: 2022-05-09 21:36:01 UTC  
> Url: https://github.com/boostorg/beast/pull/2418#issuecomment-1121606581  

Please merge this if it is ready

---
