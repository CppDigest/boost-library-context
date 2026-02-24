# #52 Add failing test for non-default constructible pair [Closed]

> Username: vinniefalco  
> Created at: 2017-10-29 18:53:35 UTC  
> Updated at: 2018-01-31 18:56:48 UTC  
> Closed at: 2018-01-31 18:56:48 UTC  
> Url: https://github.com/boostorg/type_traits/pull/52  

Reproduces #51

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-10-29 18:54:09 UTC  
> Url: https://github.com/boostorg/type_traits/pull/52#issuecomment-340285135  

@pdimov This reproduces the error described in https://github.com/boostorg/optional/issues/39

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-10-30 12:06:47 UTC  
> Url: https://github.com/boostorg/type_traits/pull/52#issuecomment-340425073  

The addition should be to `has_trivial_constr_test.cpp`, not here.

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-10-30 12:09:49 UTC  
> Url: https://github.com/boostorg/type_traits/pull/52#issuecomment-340425703  

Interesting that it passes though. I had similar trouble making it fail in https://github.com/boostorg/optional/pull/40.

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-10-30 12:18:04 UTC  
> Url: https://github.com/boostorg/type_traits/pull/52#issuecomment-340427433  

Ah, the reason it passed was that it caught the window in which Boost.Build was broken and didn't pass `-std=c++11` so everything was C++03. I've restarted it.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-10-30 12:32:36 UTC  
> Updated_at: 2017-10-30 12:34:01 UTC  
> Url: https://github.com/boostorg/type_traits/pull/52#issuecomment-340430440  

Reproduces now (as in, fails compilation on g++ 4.9), but the test fails (the trait reports true) on g++ 5 too, which is interesting.

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-10-30 13:06:44 UTC  
> Url: https://github.com/boostorg/type_traits/pull/52#issuecomment-340437936  

> The addition should be to has_trivial_constr_test.cpp, not here.  
  
https://github.com/boostorg/type_traits/pull/53  
  
Let's see if it reproduces.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2018-01-31 18:56:48 UTC  
> Url: https://github.com/boostorg/type_traits/pull/52#issuecomment-362033875  

Closing as now fixed.

---
