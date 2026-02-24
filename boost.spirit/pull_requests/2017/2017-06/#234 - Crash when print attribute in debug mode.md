# #234 Crash when print attribute in debug mode. [Merged]

> Username: glacierx  
> Created at: 2017-06-19 07:44:57 UTC  
> Updated at: 2017-06-19 10:12:28 UTC  
> Merged at: 2017-06-19 09:04:33 UTC  
> Closed at: 2017-06-19 09:04:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/234  

Please take a look. Thanks.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-06-19 09:08:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/234#issuecomment-309382585  

OK, I'll need to look into this first; reverted the merge. Could you provide a test case that exhibits the problem?

---

## Comment 2

> Username: djowel  
> Created_at: 2017-06-19 09:09:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/234#issuecomment-309382717  

(reverting this for now: https://github.com/boostorg/spirit/pull/235)

---

## Comment 3

> Username: glacierx  
> Created_at: 2017-06-19 09:50:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/234#issuecomment-309392477  

[emtpy_attribute.cpp](https://github.com/glacierx/spirit/blob/develop/test/x3/emtpy_attribute.cpp)

---

## Comment 4

> Username: djowel  
> Created_at: 2017-06-19 10:12:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/234#issuecomment-309397698  

See my comment in the other PR

---
