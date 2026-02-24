# #73 B13 - unit_test fixes [Merged]

> Username: vinniefalco  
> Created at: 2016-09-02 19:14:17 UTC  
> Updated at: 2017-07-25 17:44:16 UTC  
> Merged at: 2016-09-02 21:32:15 UTC  
> Closed at: 2016-09-02 21:32:15 UTC  
> Url: https://github.com/boostorg/beast/pull/73  

This is needed for NuDB  
  
Reviewers: @nbougalis @HowardHinnant

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-09-02 19:27:34 UTC  
> Url: https://github.com/boostorg/beast/pull/73#issuecomment-244466680  

[![Coverage Status](https://coveralls.io/builds/7729223/badge)](https://coveralls.io/builds/7729223)  
  
Coverage remained the same at 98.067% when pulling **bdfa0c64576034a0bb14078d0583bb8d5223708a on b13** into **241795cd733e2653e92090573d0ff46125329e9b on master**.

---

## Comment 2

> Username: codecov-io  
> Created_at: 2016-09-02 19:28:48 UTC  
> Updated_at: 2016-09-02 21:21:40 UTC  
> Url: https://github.com/boostorg/beast/pull/73#issuecomment-244466968  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/73?src=pr) is 98.06% (diff: 100%)  
  
> Merging [#73](https://codecov.io/gh/vinniefalco/Beast/pull/73?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will not change coverage  
  
``` diff  
@@             master        #73   diff @@  
==========================================  
  Files            71         71            
  Lines          4036       4036            
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
  Hits           3958       3958            
  Misses           78         78            
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [241795c...2f9a844](https://codecov.io/gh/vinniefalco/Beast/compare/241795cd733e2653e92090573d0ff46125329e9b...2f9a8440c2432d8a196571d6300404cb76314125?src=pr)

---

## Comment 3

> Username: HowardHinnant  
> Created_at: 2016-09-02 19:51:45 UTC  
> Updated_at: 2016-09-02 20:56:35 UTC  
> Url: https://github.com/boostorg/beast/pull/73#discussion_r77402974  

Here's the set of headers I recommend:  
  
```  
#include <ios>  
#include <memory>  
#include <ostream>  
#include <streambuf>  
#include <string>  
```  
  
You could get away with less, but there is little motivation to do so.

---

## Comment 4

> Username: HowardHinnant  
> Created_at: 2016-09-02 19:52:27 UTC  
> Url: https://github.com/boostorg/beast/pull/73#issuecomment-244472185  

Just one picky comment about headers.  Tests out on macOS.  👍

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2016-09-02 20:07:48 UTC  
> Url: https://github.com/boostorg/beast/pull/73#issuecomment-244475436  

How'd you come to that conclusion about the headers (and I'm not saying I disagree, just want to know the methodology)

---

## Comment 6

> Username: HowardHinnant  
> Created_at: 2016-09-02 20:09:01 UTC  
> Url: https://github.com/boostorg/beast/pull/73#issuecomment-244475683  

I searched for `std` and for each one I hit, I noted the header that associated identifier lives in.  Then I alphabetized the list.

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2016-09-02 20:09:06 UTC  
> Updated_at: 2016-09-02 20:56:35 UTC  
> Url: https://github.com/boostorg/beast/pull/73#discussion_r77404949  

WillFix

---

## Comment 8

> Username: coveralls  
> Created_at: 2016-09-02 20:22:13 UTC  
> Url: https://github.com/boostorg/beast/pull/73#issuecomment-244478452  

[![Coverage Status](https://coveralls.io/builds/7729967/badge)](https://coveralls.io/builds/7729967)  
  
Coverage remained the same at 98.067% when pulling **583d66a816b1f478309dbff6d2428ffc876a92fb on b13** into **241795cd733e2653e92090573d0ff46125329e9b on master**.

---

## Comment 9

> Username: nbougalis  
> Created_at: 2016-09-02 20:47:58 UTC  
> Updated_at: 2016-09-02 20:56:35 UTC  
> Url: https://github.com/boostorg/beast/pull/73#discussion_r77409675  

"if one is attached when the stream is created"

---

## Comment 10

> Username: nbougalis  
> Created_at: 2016-09-02 20:50:22 UTC  
> Url: https://github.com/boostorg/beast/pull/73#issuecomment-244484192  

Left one pico-nit about a comment. :+1:

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2016-09-02 20:59:17 UTC  
> Url: https://github.com/boostorg/beast/pull/73#issuecomment-244485994  

@nbougalis willfix / didfix

---

## Comment 12

> Username: coveralls  
> Created_at: 2016-09-02 21:11:03 UTC  
> Url: https://github.com/boostorg/beast/pull/73#issuecomment-244488361  

[![Coverage Status](https://coveralls.io/builds/7730688/badge)](https://coveralls.io/builds/7730688)  
  
Coverage remained the same at 98.067% when pulling **2f9a8440c2432d8a196571d6300404cb76314125 on b13** into **241795cd733e2653e92090573d0ff46125329e9b on master**.

---
