# #24 Medley [Closed]

> Username: vinniefalco  
> Created at: 2016-06-08 15:31:10 UTC  
> Updated at: 2017-07-25 17:44:16 UTC  
> Closed at: 2016-07-06 17:56:16 UTC  
> Url: https://github.com/boostorg/beast/pull/24  

This is a response to compilation problems encountered by a user

---

## Comment 1

> Username: codecov-io  
> Created_at: 2016-06-08 15:58:18 UTC  
> Updated_at: 2016-06-21 15:44:43 UTC  
> Url: https://github.com/boostorg/beast/pull/24#issuecomment-224637203  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/24?src=pr) is **98.01%**  
  
> Merging [#24](https://codecov.io/gh/vinniefalco/Beast/pull/24?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will decrease coverage by **0.09%**  
  
``` diff  
@@             master        #24   diff @@  
==========================================  
  Files            72         72            
  Lines          5073       5079     +6     
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
+ Hits           4977       4978     +1     
- Misses           96        101     +5     
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last updated by [999e2fa...0c340de](https://codecov.io/gh/vinniefalco/Beast/compare/999e2fa0318b5982736d3ea01a418770ea802671...0c340de237a9f15f67667d598d6a1fb947ac9ee3)

---

## Comment 2

> Username: ximinez  
> Created_at: 2016-06-20 22:35:26 UTC  
> Updated_at: 2016-06-21 15:19:29 UTC  
> Url: https://github.com/boostorg/beast/pull/24#discussion_r67779390  

This comment can be removed. The function is not deleted anymore, and you have a similar comment for the `static_assert`.

---

## Comment 3

> Username: ximinez  
> Created_at: 2016-06-20 22:49:33 UTC  
> Url: https://github.com/boostorg/beast/pull/24#issuecomment-227293269  

Looks good. :+1: I assume we'll have to wait for #29 to get the CI to pass.

---

## Comment 4

> Username: seelabs  
> Created_at: 2016-06-21 13:18:35 UTC  
> Updated_at: 2016-06-21 15:19:29 UTC  
> Url: https://github.com/boostorg/beast/pull/24#discussion_r67865470  

Why not `bool`?

---

## Comment 5

> Username: seelabs  
> Created_at: 2016-06-21 13:33:07 UTC  
> Url: https://github.com/boostorg/beast/pull/24#issuecomment-227440553  

:+1:

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2016-06-21 15:07:20 UTC  
> Updated_at: 2016-06-21 15:19:29 UTC  
> Url: https://github.com/boostorg/beast/pull/24#discussion_r67886813  

It can take on 3 values:  
  
0 - Don't skip  
1 - Skip  
2 - Is HTTP Upgrade

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2016-06-21 15:13:06 UTC  
> Updated_at: 2016-06-21 15:19:29 UTC  
> Url: https://github.com/boostorg/beast/pull/24#discussion_r67888019  

True

---

## Comment 8

> Username: seelabs  
> Created_at: 2016-06-21 15:15:01 UTC  
> Updated_at: 2016-06-21 15:19:29 UTC  
> Url: https://github.com/boostorg/beast/pull/24#discussion_r67888423  

Got it. And case (2) Isn't implemented right now? I only see `skip_body_` taking on values on 1 and 0.

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2016-06-21 15:16:46 UTC  
> Updated_at: 2016-06-21 15:19:29 UTC  
> Url: https://github.com/boostorg/beast/pull/24#discussion_r67888790  

The base class handles the return value of `on_headers`, and it takes care of values 0, 1, and 2:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/basic_parser_v1.ipp#L902

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2016-06-21 15:19:53 UTC  
> Url: https://github.com/boostorg/beast/pull/24#discussion_r67889393  

Updated

---
