# #638 refactor hier_part_rule abs path [Merged]

> Username: alandefreitas  
> Created at: 2022-12-19 18:07:14 UTC  
> Updated at: 2022-12-22 16:09:13 UTC  
> Merged at: 2022-12-21 17:55:42 UTC  
> Closed at: 2022-12-21 17:55:42 UTC  
> Url: https://github.com/boostorg/url/pull/638  

fix #636

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-19 18:14:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/638#pullrequestreview-1223327257  

📁 include/boost/url/rfc/detail/impl/hier_part_rule.ipp

```diff
  72 |+          (*it != '/' &&
  73 |+           *it != '?' &&
  74 |+           *it != '#')))
```

> Username: vinniefalco  
> Created_at: 2022-12-19 18:14:40 UTC  
> Updated_at: 2022-12-19 18:14:41 UTC  
> Url: https://github.com/boostorg/url/pull/638#discussion_r1052497237  

needs a test or two obviously

> Username: vinniefalco  
> Created_at: 2022-12-19 18:15:31 UTC  
> Url: https://github.com/boostorg/url/pull/638#discussion_r1052497874  

The spacing is weird, I would have written it as  
```  
    if(it == end || (  
        t.has_authority && (  
            *it != '/' &&  
            *it != '?' &&  
            *it != '#')))  
```  
  
This way everything is aligned on tab stops without added spaces

> Username: alandefreitas  
> Created_at: 2022-12-19 18:30:53 UTC  
> Url: https://github.com/boostorg/url/pull/638#discussion_r1052510841  

Oh! :open_mouth: Done.  
  
I forgot to bring any tests from the `format` branch. I came up with a simpler test here.

> Username: vinniefalco  
> Created_at: 2022-12-19 19:14:34 UTC  
> Url: https://github.com/boostorg/url/pull/638#discussion_r1052550425  

I like simpler tests


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-12-19 18:24:08 UTC  
> Url: https://github.com/boostorg/url/pull/638#issuecomment-1358067075  

GCOVR code coverage report [https://638.url.prtest.cppalliance.org/gcovr/index.html](https://638.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://638.url.prtest.cppalliance.org/genhtml/index.html](https://638.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://638.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://638.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-12-19 18:42:01 UTC  
> Url: https://github.com/boostorg/url/pull/638#issuecomment-1358087130  

GCOVR code coverage report [https://638.url.prtest.cppalliance.org/gcovr/index.html](https://638.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://638.url.prtest.cppalliance.org/genhtml/index.html](https://638.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://638.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://638.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-12-19 18:47:40 UTC  
> Updated_at: 2022-12-19 19:14:06 UTC  
> Url: https://github.com/boostorg/url/pull/638#issuecomment-1358092449  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/638?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#638](https://codecov.io/gh/boostorg/url/pull/638?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (019d98e) into [develop](https://codecov.io/gh/boostorg/url/commit/19b208d53d67e01c5fe44ad85851c239bcd66b7c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (19b208d) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/638/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/638?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #638   +/-   ##  
========================================  
  Coverage    96.48%   96.48%             
========================================  
  Files          138      138             
  Lines         6741     6745    +4       
========================================  
+ Hits          6504     6508    +4       
  Misses         237      237             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/638?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...clude/boost/url/rfc/detail/impl/hier\_part\_rule.ipp](https://codecov.io/gh/boostorg/url/pull/638/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2RldGFpbC9pbXBsL2hpZXJfcGFydF9ydWxlLmlwcA==) | `94.44% <100.00%> (+0.44%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/638?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/638?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [19b208d...019d98e](https://codecov.io/gh/boostorg/url/pull/638?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-12-19 19:12:30 UTC  
> Url: https://github.com/boostorg/url/pull/638#issuecomment-1358134749  

GCOVR code coverage report [https://638.url.prtest.cppalliance.org/gcovr/index.html](https://638.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://638.url.prtest.cppalliance.org/genhtml/index.html](https://638.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://638.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://638.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 6

> Username: alandefreitas  
> Created_at: 2022-12-19 19:28:41 UTC  
> Url: https://github.com/boostorg/url/pull/638#issuecomment-1358151099  

Is this good to go then?

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2022-12-21 17:31:22 UTC  
> Url: https://github.com/boostorg/url/pull/638#issuecomment-1361718188  

Yep.

---
