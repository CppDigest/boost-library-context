# #225 Remove lexical_cast<> dependencies. [Merged]

> Username: pulver  
> Created at: 2019-07-05 12:55:23 UTC  
> Updated at: 2019-07-07 16:57:12 UTC  
> Merged at: 2019-07-07 12:54:46 UTC  
> Closed at: 2019-07-07 12:54:46 UTC  
> Url: https://github.com/boostorg/math/pull/225  



---

## Comment 1

> Username: pulver  
> Created_at: 2019-07-05 13:03:28 UTC  
> Url: https://github.com/boostorg/math/pull/225#issuecomment-508751996  

The API change in this PR consists of removing the autodiff constructor:  
  
    explicit fvar(char const*)  
  
A number of Boost.Math functions call `lexical_cast<Real>()`. Adding this constructor satisfied this requirement for when `Real` is an autodiff type, but a less intrusive solution is sought after and requested.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2019-07-05 15:05:18 UTC  
> Url: https://github.com/boostorg/math/pull/225#issuecomment-508787647  

Ok, I just took a look at this and I'm happy with it. Do you think it's important to be in 1.71 or should we wait?

---

## Comment 3

> Username: pulver  
> Created_at: 2019-07-05 15:23:09 UTC  
> Url: https://github.com/boostorg/math/pull/225#issuecomment-508792840  

> Ok, I just took a look at this and I'm happy with it. Do you think it's important to be in 1.71 or should we wait?  
  
Thank you for looking. Yes, I think it's important to be included with 1.71 so we don't break the API if it were removed later. The tests have passed on my own boost.math fork: [travis](https://travis-ci.com/pulver/math/builds/118018477) [appveyor](https://ci.appveyor.com/project/pulver/math/builds/25758257).

---

## Comment 4

> Username: NAThompson  
> Created_at: 2019-07-05 17:48:26 UTC  
> Url: https://github.com/boostorg/math/pull/225#issuecomment-508826181  

The Travis build error is not your fault; I'll fix it. But there is the following warning:  
  
```  
../example/autodiff_black_scholes.cpp: In function ‘boost::math::differentiation::autodiff_v1::promote<Price, Sigma, Tau, Rate> black_scholes_option_price(CP, double, const Price&, const Sigma&, const Tau&, const Rate&) [with Price = boost::math::differentiation::autodiff_v1::detail::fvar<double, 3>; Sigma = boost::math::differentiation::autodiff_v1::detail::fvar<boost::math::differentiation::autodiff_v1::detail::fvar<double, 3>, 0>; Tau = boost::math::differentiation::autodiff_v1::detail::fvar<boost::math::differentiation::autodiff_v1::detail::fvar<boost::math::differentiation::autodiff_v1::detail::fvar<double, 1>, 0>, 0>; Rate = boost::math::differentiation::autodiff_v1::detail::fvar<boost::math::differentiation::autodiff_v1::detail::fvar<boost::math::differentiation::autodiff_v1::detail::fvar<boost::math::differentiation::autodiff_v1::detail::fvar<double, 1>, 0>, 0>, 0>]’:  
../example/autodiff_black_scholes.cpp:45:1: warning: control reaches end of non-void function [-Wreturn-type]  
 }  
 ^  
```

---

## Comment 5

> Username: pulver  
> Created_at: 2019-07-06 17:06:47 UTC  
> Updated_at: 2019-07-06 17:07:41 UTC  
> Url: https://github.com/boostorg/math/pull/225#issuecomment-508940652  

> But there is the following warning:  
>   
Fixed. Will squash/merge to `develop` if/when tests pass.

---

## Comment 6

> Username: pulver  
> Created_at: 2019-07-07 11:40:24 UTC  
> Url: https://github.com/boostorg/math/pull/225#issuecomment-508992698  

Warnings were fixed, but unrelated `catmull_rom_incl_test` and `catmull_rom_concept_test` weren't compiling in appveyor. Ok to squash&merge to `develop`?

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2019-07-07 12:08:07 UTC  
> Url: https://github.com/boostorg/math/pull/225#issuecomment-508994491  

OK by me: the CI failures in develop were my fault, they should be fixed now I hope.  Grovelling apologies all round!

---

## Comment 8

> Username: NAThompson  
> Created_at: 2019-07-07 15:22:28 UTC  
> Url: https://github.com/boostorg/math/pull/225#issuecomment-509007942  

The root cause of this madness was the false generality of using std::size() from C++17. I’ve learned my lesson from that!

---

## Comment 9

> Username: NAThompson  
> Created_at: 2019-07-07 16:57:12 UTC  
> Url: https://github.com/boostorg/math/pull/225#issuecomment-509015023  

@pulver : By the way, I want to congratulate you for putting up with all our nonsense and fighting the good fight to get this feature past the finish line. I really think it's going to be a great addition to Boost.Math.

---
