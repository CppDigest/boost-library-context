# #629 result unchecked access documentation [Merged]

> Username: alandefreitas  
> Created at: 2022-11-21 18:05:08 UTC  
> Updated at: 2022-12-22 16:08:24 UTC  
> Merged at: 2022-12-22 01:17:11 UTC  
> Closed at: 2022-12-22 01:17:11 UTC  
> Url: https://github.com/boostorg/url/pull/629  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-11-21 18:14:53 UTC  
> Url: https://github.com/boostorg/url/pull/629#issuecomment-1322466197  

GCOVR code coverage report [https://629.url.prtest.cppalliance.org/gcovr/index.html](https://629.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://629.url.prtest.cppalliance.org/genhtml/index.html](https://629.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-11-21 18:16:54 UTC  
> Url: https://github.com/boostorg/url/pull/629#issuecomment-1322468423  

An automated preview of the documentation is available at [https://629.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://629.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-11-21 18:22:21 UTC  
> Updated_at: 2022-12-22 00:13:36 UTC  
> Url: https://github.com/boostorg/url/pull/629#issuecomment-1322474371  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/629?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#629](https://codecov.io/gh/boostorg/url/pull/629?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (05c1b9e) into [develop](https://codecov.io/gh/boostorg/url/commit/79e2d055046650680a3138f4961e4562d212e4db?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (79e2d05) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/629/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/629?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #629   +/-   ##  
========================================  
  Coverage    96.48%   96.48%             
========================================  
  Files          138      138             
  Lines         6746     6746             
========================================  
  Hits          6509     6509             
  Misses         237      237             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/629?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/629?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [79e2d05...05c1b9e](https://codecov.io/gh/boostorg/url/pull/629?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-23 18:15:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/629#pullrequestreview-1192105091  

📁 test/unit/parse.cpp

```diff
  37 |+         }
  38 |+         {
  39 |+             result< url_view > ru = parse_uri_reference( "https://www.example.com/path/to/file.txt" );
```

> Username: vinniefalco  
> Created_at: 2022-11-23 18:15:07 UTC  
> Updated_at: 2022-11-23 18:15:08 UTC  
> Url: https://github.com/boostorg/url/pull/629#discussion_r1030756872  

I use `rc` as the canonical variable name.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-23 18:15:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/629#pullrequestreview-1192105616  

📁 test/unit/snippets.cpp

```diff
 282 |         if ( ru.has_value() )
 283 |+         {
 284 |+             url u = ru.value();
```

> Username: vinniefalco  
> Created_at: 2022-11-23 18:15:36 UTC  
> Updated_at: 2022-11-23 18:15:37 UTC  
> Url: https://github.com/boostorg/url/pull/629#discussion_r1030757321  

`value` is checking, if we use `has_value` then we should use `operator*` afterwards, or else we are double-checking

> Username: alandefreitas  
> Created_at: 2022-11-23 18:24:46 UTC  
> Url: https://github.com/boostorg/url/pull/629#discussion_r1030764435  

But the point of this snippet in the exposition is to differentiate them:  
  
https://629.url.prtest.cppalliance.org/libs/url/doc/html/url/urls/parsing.html

> Username: vinniefalco  
> Created_at: 2022-12-21 20:03:53 UTC  
> Updated_at: 2022-12-21 20:03:54 UTC  
> Url: https://github.com/boostorg/url/pull/629#discussion_r1054797045  

oh


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-23 18:15:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/629#pullrequestreview-1192106023  

📁 test/unit/parse.cpp

```diff
  41 |+             {
  42 |+                 url_view u = *ru;
  43 |+                 assert(u.encoded_path() == "/path/to/file.txt");
```

> Username: vinniefalco  
> Created_at: 2022-11-23 18:15:59 UTC  
> Url: https://github.com/boostorg/url/pull/629#discussion_r1030757603  

This could just be `rc->encoded_path()` and lose the local variable (and scope). Shorter examples are better.

> Username: alandefreitas  
> Created_at: 2022-11-24 00:04:43 UTC  
> Url: https://github.com/boostorg/url/pull/629#discussion_r1030952777  

This is exemplifying `result::operator*` in the exposition.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-23 18:16:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/629#pullrequestreview-1192106437  

📁 test/unit/snippets.cpp

```diff
 288 |+         boost::ignore_unused(ru);
 289 |+     }
 290 |+ 
```

> Username: vinniefalco  
> Created_at: 2022-11-23 18:16:24 UTC  
> Url: https://github.com/boostorg/url/pull/629#discussion_r1030757923  

We need all of the javadocs in parse.hpp in here, correctly labeled

> Username: alandefreitas  
> Created_at: 2022-11-23 18:25:52 UTC  
> Updated_at: 2022-11-23 18:25:53 UTC  
> Url: https://github.com/boostorg/url/pull/629#discussion_r1030765311  

The javadocs should also go in `snippets.cpp`? I thought `snippets.cpp` were just for the exposition and javadocs should go in `parse.cpp`.

> Username: vinniefalco  
> Created_at: 2022-11-23 22:57:02 UTC  
> Updated_at: 2022-11-23 22:57:03 UTC  
> Url: https://github.com/boostorg/url/pull/629#discussion_r1030926309  

Oh... I thought this was a javadoc.


---

## Comment 8

> Username: vinniefalco  
> Created_at: 2022-11-25 18:45:35 UTC  
> Url: https://github.com/boostorg/url/pull/629#issuecomment-1327784745  

merge it

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-12-21 18:31:54 UTC  
> Url: https://github.com/boostorg/url/pull/629#issuecomment-1361843618  

An automated preview of the documentation is available at [https://629.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://629.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-12-21 18:52:59 UTC  
> Url: https://github.com/boostorg/url/pull/629#issuecomment-1361892415  

GCOVR code coverage report [https://629.url.prtest.cppalliance.org/gcovr/index.html](https://629.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://629.url.prtest.cppalliance.org/genhtml/index.html](https://629.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://629.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://629.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-12-21 22:36:52 UTC  
> Url: https://github.com/boostorg/url/pull/629#issuecomment-1362186290  

An automated preview of the documentation is available at [https://629.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://629.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-12-21 22:44:22 UTC  
> Url: https://github.com/boostorg/url/pull/629#issuecomment-1362190615  

GCOVR code coverage report [https://629.url.prtest.cppalliance.org/gcovr/index.html](https://629.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://629.url.prtest.cppalliance.org/genhtml/index.html](https://629.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://629.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://629.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-12-22 00:01:51 UTC  
> Url: https://github.com/boostorg/url/pull/629#issuecomment-1362233637  

An automated preview of the documentation is available at [https://629.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://629.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2022-12-22 00:09:29 UTC  
> Url: https://github.com/boostorg/url/pull/629#issuecomment-1362238444  

GCOVR code coverage report [https://629.url.prtest.cppalliance.org/gcovr/index.html](https://629.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://629.url.prtest.cppalliance.org/genhtml/index.html](https://629.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://629.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://629.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
