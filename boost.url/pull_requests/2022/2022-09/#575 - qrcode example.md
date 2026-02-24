# #575 qrcode example [Closed]

> Username: alandefreitas  
> Created at: 2022-09-29 00:36:12 UTC  
> Updated at: 2022-10-13 04:49:52 UTC  
> Closed at: 2022-10-03 21:24:45 UTC  
> Url: https://github.com/boostorg/url/pull/575  

fix #419

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2022-09-29 00:36:37 UTC  
> Updated_at: 2022-09-29 00:36:49 UTC  
> Url: https://github.com/boostorg/url/pull/575#issuecomment-1261605558  

That example is fun to play with

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-09-29 00:46:52 UTC  
> Url: https://github.com/boostorg/url/pull/575#issuecomment-1261612246  

An automated preview of the documentation is available at [https://575.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://575.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-09-29 00:48:19 UTC  
> Url: https://github.com/boostorg/url/pull/575#issuecomment-1261613148  

GCOVR code coverage report [https://575.url.prtest.cppalliance.org/gcovr/index.html](https://575.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://575.url.prtest.cppalliance.org/genhtml/index.html](https://575.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 00:49:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/575#pullrequestreview-1124502619  

📁 doc/qbk/6.0.examples.qbk

```diff
  13 | [block'''<?dbhtml stop-chunking?>''']
  14 | 
  15 |+ [section Qr Code]
```

> Username: vinniefalco  
> Created_at: 2022-09-29 00:49:48 UTC  
> Url: https://github.com/boostorg/url/pull/575#discussion_r982970649  

QR Code


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 00:50:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/575#pullrequestreview-1124502791  

📁 example/qrcode/qrcode.cpp

```diff
   9 |+ 
  10 |+ /*
  11 |+     This example shows how to compose and mutate
```

> Username: vinniefalco  
> Created_at: 2022-09-29 00:50:09 UTC  
> Url: https://github.com/boostorg/url/pull/575#discussion_r982970775  

"construct and modify"


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 00:51:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/575#pullrequestreview-1124503198  

📁 example/qrcode/qrcode.cpp

```diff
  82 |+     std::cout << u << '\n';
  83 |+ 
  84 |+     return EXIT_SUCCESS;
```

> Username: vinniefalco  
> Created_at: 2022-09-29 00:51:00 UTC  
> Updated_at: 2022-09-29 00:51:01 UTC  
> Url: https://github.com/boostorg/url/pull/575#discussion_r982971085  

short and sweet :)

> Username: alandefreitas  
> Created_at: 2022-09-29 00:52:51 UTC  
> Url: https://github.com/boostorg/url/pull/575#discussion_r982971755  

:)


---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2022-09-29 00:53:21 UTC  
> Updated_at: 2022-09-29 17:51:32 UTC  
> Url: https://github.com/boostorg/url/pull/575#issuecomment-1261616009  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/575?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#575](https://codecov.io/gh/boostorg/url/pull/575?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (bb935a9) into [develop](https://codecov.io/gh/boostorg/url/commit/6bd4691d4c52e97275846b85945da69115c0037d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6bd4691) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/575/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/575?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #575   +/-   ##  
========================================  
  Coverage    96.76%   96.76%             
========================================  
  Files          138      138             
  Lines         6700     6700             
========================================  
  Hits          6483     6483             
  Misses         217      217             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/575?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/575?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6bd4691...bb935a9](https://codecov.io/gh/boostorg/url/pull/575?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-09-29 17:27:00 UTC  
> Url: https://github.com/boostorg/url/pull/575#issuecomment-1262586732  

An automated preview of the documentation is available at [https://575.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://575.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-09-29 17:31:50 UTC  
> Url: https://github.com/boostorg/url/pull/575#issuecomment-1262591462  

GCOVR code coverage report [https://575.url.prtest.cppalliance.org/gcovr/index.html](https://575.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://575.url.prtest.cppalliance.org/genhtml/index.html](https://575.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-09-29 17:33:49 UTC  
> Url: https://github.com/boostorg/url/pull/575#issuecomment-1262593410  

An automated preview of the documentation is available at [https://575.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://575.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2022-10-03 19:23:48 UTC  
> Url: https://github.com/boostorg/url/pull/575#issuecomment-1265925451  

good

---

## Comment 12

> Username: alandefreitas  
> Created_at: 2022-10-03 21:24:45 UTC  
> Url: https://github.com/boostorg/url/pull/575#issuecomment-1266070212  

Merged

---
