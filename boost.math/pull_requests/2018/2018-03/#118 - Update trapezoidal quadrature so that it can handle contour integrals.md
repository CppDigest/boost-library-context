# #118 Update trapezoidal quadrature so that it can handle contour integrals. [Merged]

> Username: NAThompson  
> Created at: 2018-03-16 07:35:53 UTC  
> Updated at: 2018-04-30 10:33:24 UTC  
> Merged at: 2018-04-30 07:46:43 UTC  
> Closed at: 2018-04-30 07:46:43 UTC  
> Url: https://github.com/boostorg/math/pull/118  

This is the first "app" to be built on top of complex multiprecision. It allows contour integrals to be evaluated numerically when the user specifies a contour.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2018-03-16 08:58:43 UTC  
> Updated_at: 2018-03-16 09:40:37 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-373646469  

Looks like this code change can also be applied to tanh-sinh, at which point we can compute the Lambert-W function in the complex plane. Might be worth thinking about making all the quadrature routines support integrands f:Real->{Real or Complex} now that multiprecision complex is heating up.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-03-18 19:08:46 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-374034684  

Note that M_PI is *not* part of the C++ standard, and is not supported by msvc (it's POSIX I believe).

---

## Comment 3

> Username: NAThompson  
> Created_at: 2018-03-20 03:01:46 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-374456622  

Ok, this now works for mpc and std::complex. Kicking off a build to see how widespread compiler support is for the `std::result_of<F(Real)>::type` syntax.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2018-03-31 06:25:53 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-377670209  

Very strange: I used equation 3.5.45 of [DLMF](https://dlmf.nist.gov/3.5) to evaluate `erfc` in the complex plane via trapezoidal quadrature. `std::complex<float>, std::complex<double>, std::complex<long double>, boost::multiprecision::number<boost::multiprecision::mpc_complex_backend<20>>`, and `boost::multiprecision::number<boost::multiprecision::mpc_complex_backend<30>>` all recover essentially the full precision of their types, but `mpc_complex_50` and larger only recover 30 digits. I'll try to investigate this more later.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2018-03-31 17:13:53 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-377708492  

Normally I would suspect a stray double-precision constant somewhere, but I don't see anything in your code that would cause that.

---

## Comment 6

> Username: NAThompson  
> Created_at: 2018-04-01 02:14:00 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-377737712  

Yeah, if it was a stray double precision, it should only recover 16 digits. Since it recovers 30 digits, it's a lot more interesting.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2018-04-01 08:11:15 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-377770739  

Maxed out on refinements?

---

## Comment 8

> Username: NAThompson  
> Created_at: 2018-04-01 08:44:10 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-377772301  

Nice catch, that was it!

---

## Comment 9

> Username: NAThompson  
> Created_at: 2018-04-02 00:02:54 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-377826862  

If you have an hour or so to sign off on this line of attack (or suggest another strategy) I’ll update the rest of the quadratures and hopefully pull some bugs out of the mpc wrapper.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2018-04-02 17:57:19 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-377994198  

This all looks good to me - I just have a couple of very minor comments:  
  
1) What are we going to do about the result_of/invoke_result dilemma?  We have BOOST_NO_CXX17_STD_INVOKE we can use to determine which (if either) to use, or we can use decltype.  One option would be to wrap whatever we're using inside a small traits class, and then if we want to support functors that return expression templates we can massage the return type inside the traits class later?  Off hand I don't see a way for std::result_of/invoke_result to handle expression template returns themselves.  
2) Very minor point: it would tidy the code up to typedef the return type at the start of the `trapezoidal` function, then use the typedef rather than `result_of`, or the one stray `decltype`.  
  
In short - go for it!

---

## Comment 11

> Username: NAThompson  
> Created_at: 2018-04-03 03:11:09 UTC  
> Updated_at: 2018-04-03 03:12:21 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-378114636  

> What are we going to do about the result_of/invoke_result dilemma?  
  
I used an `#ifdef` switch + `typedef` in the function + `auto` return type, which is in the spirit of your suggestion. We'll see how this actually plays out once the build completes.  
  
>  Off hand I don't see a way for std::result_of/invoke_result to handle expression template returns themselves.  
  
Nor do I. But is it desirable?  
  
As to `mpc`, these all work locally for me, in C++11 and C++17, on clang and g++-7. Once the `mpc` commit get merged into multiprecision, we can just uncomment those tests and everything should work.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2018-04-03 18:46:37 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-378355945  

Just in case you were wondering... I cancelled the appveyor CI build for the latest commit, as it's failing for config reasons.  Once Boost.Config is patched up we can restart the build.

---

## Comment 13

> Username: NAThompson  
> Created_at: 2018-04-04 01:36:10 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-378451708  

Yeah, I wanted to cancel that one myself. There's of course good reasons to do a full build, even if one of the build fails, but for the most part, it just wastes resources and shows us the same error over and over. (On boost.config, this is obviously the wrong approach, but on boost.math, it seems like it would be preferable to fail fast.) The [travis docs](https://docs.travis-ci.com/user/customizing-the-build/) aren't really helpful in this regard. Maybe `b2 -q` so that the error message is always at the end of the log?

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2018-04-05 10:06:53 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-378885591  

Tests all passed OK with updated Boost.Config.  However, I'm having second thoughts about that :(  The changes to Boost.Config disable use of `std::invoke` in a lot of cases that don't care about the `invoke_result` return type.  See comment on the end of https://github.com/boostorg/config/pull/98.  
  
I guess the alternatives are:  
  
* `typedef decltype(f(a)) result_type;`  
* `typedef decltype(std::invoke(f, a)) result_type;`  
* `auto`  
  
I think I weakly prefer having a typedef (ie not auto) because it allows for easier changes later should the need arise.  Thoughts?

---

## Comment 15

> Username: NAThompson  
> Created_at: 2018-04-05 12:26:51 UTC  
> Updated_at: 2018-04-05 12:27:18 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-378918512  

I remember that I wanted to avoid `auto` to avoid ET type pandemonium, and grabbed the first alternative I found (`std::invoke_result`). But your suggestion of `typedef decltype(f(a)) result_type;` is simpler. Let's see how the build plays out.

---

## Comment 16

> Username: NAThompson  
> Created_at: 2018-04-06 01:31:43 UTC  
> Url: https://github.com/boostorg/math/pull/118#issuecomment-379123600  

Ok the failures are timeouts so it looks good.

---
