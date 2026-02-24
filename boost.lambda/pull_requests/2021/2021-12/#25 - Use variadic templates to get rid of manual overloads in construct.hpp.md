# #25 Use variadic templates to get rid of manual overloads in construct.hpp [Merged]

> Username: RaisinTen  
> Created at: 2021-12-25 14:28:24 UTC  
> Updated at: 2021-12-28 00:52:11 UTC  
> Merged at: 2021-12-27 23:02:21 UTC  
> Closed at: 2021-12-27 23:02:21 UTC  
> Url: https://github.com/boostorg/lambda/pull/25  

There is no need to write the overloads manually and limiting our users  
to a max of 10 arguments when we can forward universal references to the  
constructors.  
  
Signed-off-by: Darshan Sen <darshan.sen@postman.com>

---

## Comment 1

> Username: RaisinTen  
> Created_at: 2021-12-25 14:42:07 UTC  
> Url: https://github.com/boostorg/lambda/pull/25#issuecomment-1001027067  

cc @pdimov

---

## Comment 2

> Username: pdimov  
> Created_at: 2021-12-26 17:14:24 UTC  
> Url: https://github.com/boostorg/lambda/pull/25#issuecomment-1001214840  

This is not going to work under C++03 and as far as I know Lambda still supports it. So it needs to be guarded with `#if !defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) && !defined(BOOST_NO_CXX11_RVALUE_REFERENCES)`.  
  
Also, please rebase it against the new develop so that CI can run.

---

## Comment 3

> Username: RaisinTen  
> Created_at: 2021-12-27 06:28:03 UTC  
> Url: https://github.com/boostorg/lambda/pull/25#issuecomment-1001362970  

@pdimov thanks, updated accordingly. PTAL.

---
