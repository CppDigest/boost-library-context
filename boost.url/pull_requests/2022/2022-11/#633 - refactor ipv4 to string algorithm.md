# #633 refactor ipv4 to string algorithm [Merged]

> Username: alandefreitas  
> Created at: 2022-11-30 00:33:24 UTC  
> Updated at: 2022-12-22 16:10:18 UTC  
> Merged at: 2022-11-30 15:00:17 UTC  
> Closed at: 2022-11-30 15:00:17 UTC  
> Url: https://github.com/boostorg/url/pull/633  

fix #632

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-11-30 00:46:57 UTC  
> Url: https://github.com/boostorg/url/pull/633#issuecomment-1331502784  

GCOVR code coverage report [https://633.url.prtest.cppalliance.org/gcovr/index.html](https://633.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://633.url.prtest.cppalliance.org/genhtml/index.html](https://633.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-30 03:44:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/633#pullrequestreview-1198645550  

📁 include/boost/url/impl/ipv4_address.ipp

```diff
 121 |+                 v %= 10;
 122 |             }
 123 |             if(v >= 10)
```

> Username: vinniefalco  
> Created_at: 2022-11-30 03:44:24 UTC  
> Updated_at: 2022-11-30 03:44:25 UTC  
> Url: https://github.com/boostorg/url/pull/633#discussion_r1035499891  

shouldn't this be "else" ?

> Username: vinniefalco  
> Created_at: 2022-11-30 04:18:46 UTC  
> Url: https://github.com/boostorg/url/pull/633#discussion_r1035512953  

```  
if( v >= 100 )  
{  
  // output three digits  
}  
else if( v >= 10 )  
{  
  // output two digits  
}  
else  
{  
  // output one digit  
}  
```

> Username: alandefreitas  
> Created_at: 2022-11-30 04:21:32 UTC  
> Url: https://github.com/boostorg/url/pull/633#discussion_r1035514031  

Oh...

> Username: alandefreitas  
> Created_at: 2022-11-30 05:09:15 UTC  
> Updated_at: 2022-11-30 05:09:25 UTC  
> Url: https://github.com/boostorg/url/pull/633#discussion_r1035534399  

Did this snippet come before or after my "Oh"? I think the way I did it is almost the same but I think it's less redundant because the last bit (`// output one digit`) always needs to happen anyway. Unless I'm missing some cases.


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-11-30 04:32:15 UTC  
> Url: https://github.com/boostorg/url/pull/633#issuecomment-1331631940  

GCOVR code coverage report [https://633.url.prtest.cppalliance.org/gcovr/index.html](https://633.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://633.url.prtest.cppalliance.org/genhtml/index.html](https://633.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-11-30 04:37:56 UTC  
> Updated_at: 2022-11-30 04:38:51 UTC  
> Url: https://github.com/boostorg/url/pull/633#issuecomment-1331634519  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/633?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#633](https://codecov.io/gh/boostorg/url/pull/633?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8eabed8) into [develop](https://codecov.io/gh/boostorg/url/commit/1d87343ea756b669baf836914d4b1d78cdba5ddb?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1d87343) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/633/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/633?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #633   +/-   ##  
========================================  
  Coverage    96.48%   96.48%             
========================================  
  Files          138      138             
  Lines         6738     6741    +3       
========================================  
+ Hits          6501     6504    +3       
  Misses         237      237             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/633?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/ipv4\_address.ipp](https://codecov.io/gh/boostorg/url/pull/633/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9pcHY0X2FkZHJlc3MuaXBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/633?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/633?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1d87343...8eabed8](https://codecov.io/gh/boostorg/url/pull/633?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
