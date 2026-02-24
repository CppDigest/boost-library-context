# #583 allow escaped null in urls [Merged]

> Username: alandefreitas  
> Created at: 2022-10-07 20:22:40 UTC  
> Updated at: 2022-12-22 16:19:50 UTC  
> Merged at: 2022-10-21 19:12:24 UTC  
> Closed at: 2022-10-21 19:12:24 UTC  
> Url: https://github.com/boostorg/url/pull/583  

fix #471

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-07 20:35:13 UTC  
> Url: https://github.com/boostorg/url/pull/583#issuecomment-1272066693  

GCOVR code coverage report [https://583.url.prtest.cppalliance.org/gcovr/index.html](https://583.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://583.url.prtest.cppalliance.org/genhtml/index.html](https://583.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-10-07 20:40:27 UTC  
> Updated_at: 2022-10-21 19:10:41 UTC  
> Url: https://github.com/boostorg/url/pull/583#issuecomment-1272070183  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/583?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#583](https://codecov.io/gh/boostorg/url/pull/583?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (521298d) into [develop](https://codecov.io/gh/boostorg/url/commit/38c7773ae010b226d64ef228dbe928b3b2b4a618?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (38c7773) will **increase** coverage by `0.02%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/583/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/583?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #583      +/-   ##  
===========================================  
+ Coverage    96.75%   96.78%   +0.02%       
===========================================  
  Files          137      137                
  Lines         6696     6693       -3       
===========================================  
- Hits          6479     6478       -1       
+ Misses         217      215       -2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/583?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/rfc/impl/query\_rule.ipp](https://codecov.io/gh/boostorg/url/pull/583/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvcXVlcnlfcnVsZS5pcHA=) | `100.00% <ø> (+6.06%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/583?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/583?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [38c7773...521298d](https://codecov.io/gh/boostorg/url/pull/583?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-07 21:29:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/583#pullrequestreview-1135088616  

📁 include/boost/url/rfc/impl/query_rule.ipp

```diff
  70 |-                 // null in input
  71 |-                 BOOST_URL_RETURN_EC(
  72 |-                     error::illegal_null);
```

> Username: vinniefalco  
> Created_at: 2022-10-07 21:29:07 UTC  
> Updated_at: 2022-10-07 21:29:08 UTC  
> Url: https://github.com/boostorg/url/pull/583#discussion_r990509720  

Are nulls allowed in `application/x-www-form-urlencoded`?

> Username: alandefreitas  
> Created_at: 2022-10-07 22:18:34 UTC  
> Url: https://github.com/boostorg/url/pull/583#discussion_r990529314  

I don't know. I would guess it's valid in certain cases. But the problem, in this case, is that query_rule is not being used for `application/x-www-form-urlencoded` only. It's used for queries in general and `url`s with nulls cannot be parsed unless `query_rule` accepts nulls. Organizing the query into key/values is also common in schemes other than http.

> Username: vinniefalco  
> Created_at: 2022-10-08 00:05:42 UTC  
> Url: https://github.com/boostorg/url/pull/583#discussion_r990557431  

> I don't know.  
  
Well what does the spec say

> Username: alandefreitas  
> Created_at: 2022-10-09 20:16:26 UTC  
> Updated_at: 2022-10-09 20:17:20 UTC  
> Url: https://github.com/boostorg/url/pull/583#discussion_r990834375  

The URI spec says it's OK. The HTTP spec doesn't say anything.  
  
The reason it can't be forbidden is that they need a way to transfer raw data that might include `%00`.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-07 21:31:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/583#pullrequestreview-1135090072  

📁 test/unit/segments_view.cpp

```diff
 133 | 
 134 |+     void
 135 |+     testNull()
```

> Username: vinniefalco  
> Created_at: 2022-10-07 21:31:36 UTC  
> Url: https://github.com/boostorg/url/pull/583#discussion_r990510802  

This is the wrong file you are spreading stuff having to do with queries, paths, fragments, etc.. all in this file which has to do with segments.


---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-10-07 22:33:20 UTC  
> Url: https://github.com/boostorg/url/pull/583#issuecomment-1272135866  

GCOVR code coverage report [https://583.url.prtest.cppalliance.org/gcovr/index.html](https://583.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://583.url.prtest.cppalliance.org/genhtml/index.html](https://583.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-09 20:39:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/583#pullrequestreview-1135385423  

📁 test/unit/url.cpp

```diff
1108 |+         BOOST_TEST_NE(p, ep);
1109 |+         BOOST_TEST_NOT(stl_equal(p, ep));
1110 |+         ep.assign({'/', 'p', 'a', '\0', 't', 'h'});
```

> Username: vinniefalco  
> Created_at: 2022-10-09 20:39:52 UTC  
> Updated_at: 2022-10-09 20:40:16 UTC  
> Url: https://github.com/boostorg/url/pull/583#discussion_r990837037  

Could you instead write  
```  
ep = std::string( "/pa\0th", 6 );  
```  
? or something similar maybe  
```  
ep = { "/pa\0th", 6 };  
```  
(not sure about that last one)


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-10-09 21:02:03 UTC  
> Url: https://github.com/boostorg/url/pull/583#issuecomment-1272627424  

GCOVR code coverage report [https://583.url.prtest.cppalliance.org/gcovr/index.html](https://583.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://583.url.prtest.cppalliance.org/genhtml/index.html](https://583.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-10-12 23:37:08 UTC  
> Url: https://github.com/boostorg/url/pull/583#issuecomment-1276846728  

GCOVR code coverage report [https://583.url.prtest.cppalliance.org/gcovr/index.html](https://583.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://583.url.prtest.cppalliance.org/genhtml/index.html](https://583.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-10-19 23:48:40 UTC  
> Url: https://github.com/boostorg/url/pull/583#issuecomment-1284711129  

GCOVR code coverage report [https://583.url.prtest.cppalliance.org/gcovr/index.html](https://583.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://583.url.prtest.cppalliance.org/genhtml/index.html](https://583.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2022-10-21 17:10:11 UTC  
> Url: https://github.com/boostorg/url/pull/583#issuecomment-1287230127  

rebase and merge

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-10-21 18:49:22 UTC  
> Url: https://github.com/boostorg/url/pull/583#issuecomment-1287324496  

GCOVR code coverage report [https://583.url.prtest.cppalliance.org/gcovr/index.html](https://583.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://583.url.prtest.cppalliance.org/genhtml/index.html](https://583.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-10-21 19:03:40 UTC  
> Url: https://github.com/boostorg/url/pull/583#issuecomment-1287336177  

GCOVR code coverage report [https://583.url.prtest.cppalliance.org/gcovr/index.html](https://583.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://583.url.prtest.cppalliance.org/genhtml/index.html](https://583.url.prtest.cppalliance.org/genhtml/index.html)

---
