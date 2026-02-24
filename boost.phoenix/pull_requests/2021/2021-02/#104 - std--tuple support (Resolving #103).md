# #104 std::tuple support (Resolving #103) [Merged]

> Username: beojan  
> Created at: 2021-02-03 14:43:29 UTC  
> Updated at: 2022-12-25 21:59:52 UTC  
> Merged at: 2021-03-11 21:59:28 UTC  
> Closed at: 2021-03-11 21:59:28 UTC  
> Url: https://github.com/boostorg/phoenix/pull/104  

This adds a `get_` lazy function that's essentially an adapted `std::get`.  
Instead of providing an `unpack` lazy function, I defined `boost::phoenix::placeholders::uarg1` through `uarg10` (actually `BOOST_PHOENIX_ARG_LIMIT`) that unpack `arg1`.

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2021-02-03 19:45:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/phoenix/pull/104#pullrequestreview-582738779  

📁 test/tuple/tuple.cpp

```diff
  33 |+     tuple_get_test();
  34 |+     boost::report_errors();
  35 |+     return 0;
```

> Username: Kojoley  
> Created_at: 2021-02-03 19:45:25 UTC  
> Updated_at: 2021-03-10 10:33:43 UTC  
> Url: https://github.com/boostorg/phoenix/pull/104#discussion_r569699496  

The right usage is `return boost::report_errors();` otherwise the test will pass even if there is an error.

> Username: beojan  
> Created_at: 2021-02-03 19:48:10 UTC  
> Updated_at: 2021-03-10 10:33:43 UTC  
> Url: https://github.com/boostorg/phoenix/pull/104#discussion_r569701232  

Oops. Fixed.


---

## Comment 2

> Username: beojan  
> Created_at: 2021-02-12 19:48:00 UTC  
> Url: https://github.com/boostorg/phoenix/pull/104#issuecomment-778415455  

The remaining AppVeyor failures appear to be a configuration issue (it can't find the compiler).

---

## Comment 3

> Username: beojan  
> Created_at: 2021-02-16 14:22:05 UTC  
> Url: https://github.com/boostorg/phoenix/pull/104#issuecomment-779867700  

The remaining Travis CI failures also seem to be configuration issues for the macOS CI.

---

## Comment 4

> Username: djowel  
> Created_at: 2021-02-16 22:40:12 UTC  
> Url: https://github.com/boostorg/phoenix/pull/104#issuecomment-780163744  

Odd failures!

---

## Comment 5

> Username: beojan  
> Created_at: 2021-02-23 09:30:28 UTC  
> Url: https://github.com/boostorg/phoenix/pull/104#issuecomment-784034239  

For Travis: I think you need to update the macOS version used to Mojave or later for Homebrew support  
For Appveyor: #105 is to fix this, I think

---

## Review 6 [Commented]

> Username: djowel  
> Created_at: 2021-03-09 23:35:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/phoenix/pull/104#pullrequestreview-608205898  

📁 include/boost/phoenix/stl/tuple.hpp

```diff
  25 |+ // Cribbing from functions in object
  26 |+ 
  27 |+ namespace boost {
```

> Username: djowel  
> Created_at: 2021-03-09 23:35:41 UTC  
> Updated_at: 2021-03-10 10:33:43 UTC  
> Url: https://github.com/boostorg/phoenix/pull/104#discussion_r590826568  

Please copy the coding style of the codebase. See the other header files.

> Username: beojan  
> Created_at: 2021-03-10 10:35:19 UTC  
> Url: https://github.com/boostorg/phoenix/pull/104#discussion_r591335142  

I think I've addressed this. Let me know if there are other changes required.


---

## Comment 7

> Username: djowel  
> Created_at: 2021-03-11 21:59:41 UTC  
> Url: https://github.com/boostorg/phoenix/pull/104#issuecomment-797078083  

Merged. Thanks for your contribution!

---

## Review 8 [Commented]

> Username: jcelerier  
> Created_at: 2022-12-25 21:47:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/phoenix/pull/104#pullrequestreview-1229778470  

📁 include/boost/phoenix/stl/tuple.hpp

```diff
 111 |+         #define BOOST_PP_LOCAL_LIMITS (1, BOOST_PHOENIX_ARG_LIMIT)
 112 |+         #define BOOST_PP_LOCAL_MACRO(N)                                                \
 113 |+             auto uarg##N =                                                             \
```

> Username: jcelerier  
> Created_at: 2022-12-25 21:47:10 UTC  
> Updated_at: 2022-12-25 21:47:34 UTC  
> Url: https://github.com/boostorg/phoenix/pull/104#discussion_r1057001194  

This causes a multiple definition error as soon as multiple files include this @djowel

> Username: beojan  
> Created_at: 2022-12-25 21:59:52 UTC  
> Url: https://github.com/boostorg/phoenix/pull/104#discussion_r1057002187  

See #112 which fixes this.


---
