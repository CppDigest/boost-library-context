# #577 update remove_origin [Closed]

> Username: alandefreitas  
> Created at: 2022-09-30 23:49:47 UTC  
> Updated at: 2022-10-13 04:50:04 UTC  
> Closed at: 2022-10-03 20:49:46 UTC  
> Url: https://github.com/boostorg/url/pull/577  

fix #394

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-30 23:52:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/577#pullrequestreview-1127435434  

📁 include/boost/url/impl/url_base.ipp

```diff
 999 |-     impl_.split(id_host, 0);
1000 |-     impl_.split(id_port, 0);
 934 |+     remove_authority();
```

> Username: vinniefalco  
> Created_at: 2022-09-30 23:52:18 UTC  
> Url: https://github.com/boostorg/url/pull/577#discussion_r985005403  

just leave a comment saying that it does 2 memmoves instead of 1


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-09-30 23:57:54 UTC  
> Url: https://github.com/boostorg/url/pull/577#issuecomment-1264137623  

GCOVR code coverage report [https://577.url.prtest.cppalliance.org/gcovr/index.html](https://577.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://577.url.prtest.cppalliance.org/genhtml/index.html](https://577.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-10-01 00:06:40 UTC  
> Updated_at: 2022-10-03 20:48:59 UTC  
> Url: https://github.com/boostorg/url/pull/577#issuecomment-1264142874  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/577?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#577](https://codecov.io/gh/boostorg/url/pull/577?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (99e18f7) into [develop](https://codecov.io/gh/boostorg/url/commit/6bd4691d4c52e97275846b85945da69115c0037d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6bd4691) will **increase** coverage by `0.04%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 99e18f7 differs from pull request most recent head 8220b34. Consider uploading reports for the commit 8220b34 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/577/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/577?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #577      +/-   ##  
===========================================  
+ Coverage    96.76%   96.80%   +0.04%       
===========================================  
  Files          138      138                
  Lines         6700     6662      -38       
===========================================  
- Hits          6483     6449      -34       
+ Misses         217      213       -4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/577?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/577/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `99.01% <100.00%> (+0.31%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/577?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/577?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6bd4691...8220b34](https://codecov.io/gh/boostorg/url/pull/577?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-10-01 00:48:01 UTC  
> Url: https://github.com/boostorg/url/pull/577#issuecomment-1264174929  

GCOVR code coverage report [https://577.url.prtest.cppalliance.org/gcovr/index.html](https://577.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://577.url.prtest.cppalliance.org/genhtml/index.html](https://577.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-03 19:20:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/577#pullrequestreview-1128895339  

📁 include/boost/url/impl/url_base.ipp

```diff
 999 |-     impl_.split(id_host, 0);
1000 |-     impl_.split(id_port, 0);
 934 |+     // this function does 2 memmoves instead of 1
```

> Username: vinniefalco  
> Created_at: 2022-10-03 19:20:05 UTC  
> Url: https://github.com/boostorg/url/pull/577#discussion_r986140097  

"these two calls perform 2 memmoves instead of 1"


---

## Comment 6

> Username: alandefreitas  
> Created_at: 2022-10-03 20:49:46 UTC  
> Url: https://github.com/boostorg/url/pull/577#issuecomment-1266030774  

Merged

---
