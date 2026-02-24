# #31 Examples, HTTP fixes [Closed]

> Username: vinniefalco  
> Created at: 2016-06-20 15:18:32 UTC  
> Updated at: 2017-07-25 17:44:16 UTC  
> Closed at: 2016-07-06 17:55:52 UTC  
> Url: https://github.com/boostorg/beast/pull/31  

Addresses #30   
- Fix prepare by calling init. prepare() can throw depending on the  
  implementation of Writer. Publicly provided beast::http writers never throw.  
- Fixes to example HTTP server  
- Fully qualify ambiguous calls to read and parse  
- Remove deprecated http::stream wrapper  
- Example HTTP server now calculates the MIME-type

---

## Comment 1

> Username: codecov-io  
> Created_at: 2016-06-20 15:24:47 UTC  
> Updated_at: 2016-06-21 20:50:37 UTC  
> Url: https://github.com/boostorg/beast/pull/31#issuecomment-227175673  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/31?src=pr) is **97.99%**  
  
> Merging [#31](https://codecov.io/gh/vinniefalco/Beast/pull/31?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will decrease coverage by **0.11%**  
  
``` diff  
@@             master        #31   diff @@  
==========================================  
  Files            72         72            
  Lines          5073       5077     +4     
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
- Hits           4977       4975     -2     
- Misses           96        102     +6     
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last updated by [999e2fa...be1782d](https://codecov.io/gh/vinniefalco/Beast/compare/999e2fa0318b5982736d3ea01a418770ea802671...be1782d52c1deb29f36963ad3f8fdf6251cf2e78)

---

## Comment 2

> Username: scottschurr  
> Created_at: 2016-06-21 14:48:49 UTC  
> Updated_at: 2016-06-21 20:26:24 UTC  
> Url: https://github.com/boostorg/beast/pull/31#discussion_r67883021  

Double checking that you really want `log_args()` to be `public`.  It side-steps the thread safety that you so nicely put into `log()`.  Your call.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2016-06-21 15:06:48 UTC  
> Updated_at: 2016-06-21 20:26:24 UTC  
> Url: https://github.com/boostorg/beast/pull/31#discussion_r67886702  

I didn't really pay it any mind since its example code and not a public interface, but I can change it.

---

## Comment 4

> Username: scottschurr  
> Created_at: 2016-06-21 15:07:48 UTC  
> Updated_at: 2016-06-21 20:26:24 UTC  
> Url: https://github.com/boostorg/beast/pull/31#discussion_r67886922  

Should the argument be `std::forward<Function>(f)`?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2016-06-21 15:10:01 UTC  
> Updated_at: 2016-06-21 20:26:24 UTC  
> Url: https://github.com/boostorg/beast/pull/31#discussion_r67887388  

Definitely not. The intent here is that `f` can be passed as either `Function const&` or `Function&`. Ownership is not transferred.  
  
See  
http://www.boost.org/doc/libs/1_61_0/doc/html/boost_asio/reference/asio_handler_invoke.html

---

## Comment 6

> Username: scottschurr  
> Created_at: 2016-06-21 15:16:43 UTC  
> Updated_at: 2016-06-21 20:26:24 UTC  
> Url: https://github.com/boostorg/beast/pull/31#discussion_r67888777  

I don't believe this multi-argument constructor needs to be marked `explicit`, but there's no harm to it.

---

## Comment 7

> Username: scottschurr  
> Created_at: 2016-06-21 16:08:05 UTC  
> Updated_at: 2016-06-21 20:26:24 UTC  
> Url: https://github.com/boostorg/beast/pull/31#discussion_r67899645  

If you do choose to make `http_async_server::log_args()` private, consider making this `log_args()` private as well.

---

## Comment 8

> Username: scottschurr  
> Created_at: 2016-06-21 16:24:22 UTC  
> Updated_at: 2016-06-21 20:26:24 UTC  
> Url: https://github.com/boostorg/beast/pull/31#discussion_r67902441  

The tiniest of nits: I'd move the `sb` declaration down close to the first use on line 49.  Your call.

---

## Comment 9

> Username: scottschurr  
> Created_at: 2016-06-21 16:26:56 UTC  
> Url: https://github.com/boostorg/beast/pull/31#issuecomment-227494734  

:+1:

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2016-06-21 16:35:45 UTC  
> Updated_at: 2016-06-21 20:26:24 UTC  
> Url: https://github.com/boostorg/beast/pull/31#discussion_r67904348  

Willfix

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2016-06-21 16:35:52 UTC  
> Updated_at: 2016-06-21 20:26:24 UTC  
> Url: https://github.com/boostorg/beast/pull/31#discussion_r67904369  

Willfix

---

## Comment 12

> Username: vinniefalco  
> Created_at: 2016-06-21 16:35:55 UTC  
> Updated_at: 2016-06-21 20:26:24 UTC  
> Url: https://github.com/boostorg/beast/pull/31#discussion_r67904375  

Willfix

---

## Comment 13

> Username: HowardHinnant  
> Created_at: 2016-06-21 20:20:59 UTC  
> Updated_at: 2016-06-21 20:26:24 UTC  
> Url: https://github.com/boostorg/beast/pull/31#discussion_r67943966  

This is wasteful.  `cerr` already flushes after each output operation.

---

## Comment 14

> Username: HowardHinnant  
> Created_at: 2016-06-21 20:24:15 UTC  
> Url: https://github.com/boostorg/beast/pull/31#issuecomment-227560366  

👍 Only one nitpicky comment.  Tests pass on OS X with `-std=c++11`.

---

## Comment 15

> Username: vinniefalco  
> Created_at: 2016-06-21 20:27:38 UTC  
> Url: https://github.com/boostorg/beast/pull/31#discussion_r67945148  

Willfix

---
