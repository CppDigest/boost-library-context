# #24 Added constexpr support to min/max functions of random number generators. Fixes #12251 [Closed]

> Username: gblanco92  
> Created at: 2016-06-03 17:29:27 UTC  
> Updated at: 2020-07-07 23:37:14 UTC  
> Closed at: 2020-07-07 23:37:14 UTC  
> Url: https://github.com/boostorg/random/pull/24  

This fixes https://svn.boost.org/trac/boost/ticket/12251

---

## Comment 1

> Username: mclow  
> Created_at: 2016-06-03 22:00:54 UTC  
> Updated_at: 2016-06-03 22:01:01 UTC  
> Url: https://github.com/boostorg/random/pull/24#issuecomment-223705163  

The code changes look good, but there are no tests!

---

## Comment 2

> Username: gblanco92  
> Created_at: 2016-06-07 13:20:26 UTC  
> Url: https://github.com/boostorg/random/pull/24#issuecomment-224277824  

I will add tests for this!

---

## Comment 3

> Username: mclow  
> Created_at: 2017-05-04 01:06:25 UTC  
> Url: https://github.com/boostorg/random/pull/24#issuecomment-299076306  

Ping?

---

## Comment 4

> Username: rtimmons  
> Created_at: 2019-03-22 14:34:24 UTC  
> Url: https://github.com/boostorg/random/pull/24#issuecomment-475644218  

Is there any way to get this merged?  
  
I've got a project that wants to use cross-platform `std::stuffle` and the lack of `constexpr min()` and `constexpr max()` on `boost::random::mt19937_64` and friends leads to compiler errors  
  
```  
.../usr/include/c++/v1/algorithm:2950:57: error:  
  constexpr variable '_Rp' must be initialized by a constant expression  
    static _LIBCPP_CONSTEXPR const _Working_result_type _Rp = _Engine::max() - _Engine::min()  
```  
  
I've verified that this change fixes the compiler error at least for my limited selection of platforms (latest gcc and clang on osx and a few linuxes).

---

## Comment 5

> Username: mclow  
> Created_at: 2019-03-22 15:14:32 UTC  
> Url: https://github.com/boostorg/random/pull/24#issuecomment-475659705  

Still has no tests....

---

## Comment 6

> Username: rtimmons  
> Created_at: 2019-03-22 15:41:16 UTC  
> Url: https://github.com/boostorg/random/pull/24#issuecomment-475669959  

Thanks @mclow - do you have any input on how this should be tested to satisfaction?  
  
-   My instincts say it's enough to e.g. assert `additive_combine::min()` (and similar) can be evaluated in a constexpr context.   
  
    Is there a way to do this that respects the `BOOST_CONSTEXPR` macro? Is it sufficient to rely on the `_NO_*_CONSTEXPR` defs?  
  
    ```c++  
    #if !defined(BOOST_NO_CXX11_CONSTEXPR) && !defined(BOOST_NO_CXX14_CONSTEXPR)  
    // test that xyz::min(),xyz::max() can be evaluated in constexpr context  
    #endif  
    ```  
  
- Alternatively we could assert that `std::shuffle` or other STL algorithms work with these RNGs. But that may not be direct enough for this and/or depends on a particular STL requiring `min/max` to be `constexpr` (which [the reference](https://en.cppreference.com/w/cpp/named_req/UniformRandomBitGenerator) doesn't actually seem to require)

---

## Comment 7

> Username: mclow  
> Created_at: 2019-03-22 15:45:25 UTC  
> Url: https://github.com/boostorg/random/pull/24#issuecomment-475671574  

> My instincts say it's enough to e.g. assert additive_combine::min() (and similar) can be evaluated in a constexpr context.  
  
Can they be evaluated, and do they return a sane answer.  
`static_assert` is probably your friend here.

---
