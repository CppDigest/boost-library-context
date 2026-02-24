# #1100 Replace redefinition of Boost.Config macros in Standalone mode [Merged]

> Username: mborland  
> Created at: 2024-02-22 07:54:39 UTC  
> Updated at: 2024-02-24 10:15:56 UTC  
> Merged at: 2024-02-22 16:04:30 UTC  
> Closed at: 2024-02-22 16:04:30 UTC  
> Url: https://github.com/boostorg/math/pull/1100  

Create our own definition of everything instead. Also add note on standalone mode usage, and update warning banner about C++14 and MSVC 14.0.   
  
Closes: #1098

---

## Comment 1

> Username: mborland  
> Created_at: 2024-02-22 10:00:26 UTC  
> Url: https://github.com/boostorg/math/pull/1100#issuecomment-1959093460  

@jzmaddock and @ckormanyos Any issues with this PR? It's mostly bulk replacement of macros to alleviate Glen's concerns about support two different consumption modes. Once support for MSVC 14.0 is dropped in 2 releases there's a fair bit of simplification that can be made regarding all the NO_CXX14_CONSTEXPR macros.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-02-22 13:08:33 UTC  
> Url: https://github.com/boostorg/math/pull/1100#issuecomment-1959423807  

Thanks for this Matt, it's what I would have done, please do go ahead and merge.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2024-02-22 16:32:39 UTC  
> Url: https://github.com/boostorg/math/pull/1100#issuecomment-1959821780  

Hi @mborland it is great. Remarkable speed getting this into 1.85. Thanks. I was only able to take a cursory glance due to high time pressure in other areas. From the things I saw and CI green, looks great.  
  
I'll run standalone stuff in a bunch of other projects on Modular-Boost-develop in my nightly tonight in other places. So that'll be an independent check...  
  
Cc: @jzmaddock

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2024-02-23 06:29:06 UTC  
> Updated_at: 2024-02-23 06:41:07 UTC  
> Url: https://github.com/boostorg/math/pull/1100#issuecomment-1960796839  

Hi Matt (@mborland)  
  
Bit of a long story here, so please bear with me...  
  
I did run into [problems](https://github.com/ckormanyos/real-time-cpp/actions/runs/8012557843/job/21888061959) with this new standalone config.  
  
The first set of problems were caused all by myself. I had been mixing standalone with non-standalone macros and also including the regular `<boost/config.hpp>`  
  
So now if I define `BOOST_MATH_STANDALONE`, then I need to exclusively use `BOOST_MATH_something` macros and **not** include `<boost/config.hpp>`. This is fine with me, but some clients may have also been wrognly mixing these. And there might be activity on lists or GH clearing this up.  
  
The final hit was mixing math/multiprecision standalone, where you run into the fact that multiprecision standalone [requires](https://github.com/boostorg/multiprecision/blob/27666619b85f014e6724c0343923b77b10ddb704/include/boost/multiprecision/detail/standalone_config.hpp#L21) inclusion of `<boost/config.hpp>`. So now you can't mix math/multiprecision both standalone. That does not go. And we can't roll out like this.  
  
The problem is, I was unable to fix it quickly. I am only able to report on it.  
  
Cc: @jzmaddock and @NAThompson

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2024-02-23 06:40:53 UTC  
> Updated_at: 2024-02-23 06:41:28 UTC  
> Url: https://github.com/boostorg/math/pull/1100#issuecomment-1960805834  

The story then gets a bit more complicated. If I then include `<boost/config.hpp>` to make multiprecision standalone _happy_, then there are conflicting macros with the new config ways of math. It's all solvable, but it looks like a bit of unwinding and sync is needed between the two entities math/multiprecision.  
  
Cc: @mborland and @jzmaddock and @NAThompson

---

## Comment 6

> Username: mborland  
> Created_at: 2024-02-23 07:09:45 UTC  
> Url: https://github.com/boostorg/math/pull/1100#issuecomment-1960829035  

Let me take a look. It seems as though previously combining standalone math and standalone multiprecision would have the same macro redefinition errors as the original issue since config is bundled into the standalone multiprecision package. Now that math does not use any of the same macro names as config this should not be an issue.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2024-02-24 10:15:54 UTC  
> Url: https://github.com/boostorg/math/pull/1100#issuecomment-1962319460  

Hi Matt (@mborland) when I straightened out the compiler options to use exclusively `BOOST_MATH_whatever` and _also_ has `<boost/config.hpp>` present for detection when building Multiprecision standalone, all my metal standalone benchmarks [work again](https://github.com/ckormanyos/real-time-cpp/actions/runs/8029792844).  
  
There seems to be no issue and everything is OK. It was my mistake.  
  
Thanks  
  
Cc: @jzmaddock and @NAThompson

---
