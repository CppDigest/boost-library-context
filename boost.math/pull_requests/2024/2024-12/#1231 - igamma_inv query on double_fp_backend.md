# #1231 igamma_inv query on double_fp_backend [Closed]

> Username: ckormanyos  
> Created at: 2024-12-28 11:27:28 UTC  
> Updated at: 2025-05-04 08:50:02 UTC  
> Closed at: 2024-12-29 09:28:35 UTC  
> Url: https://github.com/boostorg/math/pull/1231  

Hi John (@jzmaddock)  
  
This may (hopefully) be my last query on _specfun_ for the newly proposed `cpp_double_fp_backend`.  
  
The final problems that the `cpp_double_fp_backend<double>` backend are facing in the _specfun_ test-suite arise in the tests of `igamma_inv`. The Halley-iteration in some (very few like two or three) cases would run and not exit (until max-Halley-iterations was reached and throw). The culprit was the exact floating-point delta-comparison needed for exit condition.  
  
In the iteration code, I replaced the exact difference of `(f - p)` with either a relative difference or an exact difference. I do not know why, but the `cpp_double_fp_backend` would sometimes have 1-eps or so of convergence _fuzz_ that I could neither explain nor eliminate in two or three `igamma_inv` cases.  
  
The one, single change you find proposed in `igamma_inverse.hpp` works around this problem. This change seems somewhat bold to me and I'm not exactly sure why my new double-double backend was experiencing 1-eps _fuzz_. The change was harmless for `cpp_dec_float` and `cpp_bin_float` tests locally.  
  
And I'm now running this change through Math's CI and requesting your opinion on this change?  
  
Cc: @mborland

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2024-12-28 11:40:45 UTC  
> Url: https://github.com/boostorg/math/pull/1231#issuecomment-2564303742  

One additional comment:  
  
I believe, when iterating for `igamma_inv()`, the actual `tgamma(a)` function is called with the same argument multiple times for each iteration trip. I think there can be difficulties when caching a pre-computed value in header-only, thread-safe. Furthermore, `tgamma` is not very costly, but it did catch my eye --- or so I believe it to have done.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-12-28 11:56:49 UTC  
> Url: https://github.com/boostorg/math/pull/1231#issuecomment-2564306585  

>The one, single change you find proposed in igamma_inverse.hpp works around this problem. This change seems somewhat bold to me and I'm not exactly sure why my new double-double backend was experiencing 1-eps fuzz. The change was harmless for cpp_dec_float and cpp_bin_float tests locally.  
  
>And I'm now running this change through Math's CI and requesting your opinion on this change?  
  
Hmm, it might end up being necessary, but I don't really like it, and in any case the root finders should be able to cope with a certain amount of random-fuzz down at the sharp end.  We shouldn't really be trying to iterate down to the last epsilon anyway for that very reason.  Do you have a test case?  
  
>I believe, when iterating for igamma_inv(), the actual tgamma(a) function is called with the same argument multiple times for each iteration trip.   
  
It shouldn't be, the functor should be called exactly once per iteration.  
  
>I think there can be difficulties when caching a pre-computed value in header-only, thread-safe.   
  
Caching where?

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2024-12-28 12:39:33 UTC  
> Updated_at: 2024-12-28 12:41:38 UTC  
> Url: https://github.com/boostorg/math/pull/1231#issuecomment-2564315800  

>> and requesting your opinion on this change?  
  
> Hmm, it might end up being necessary, but I don't really like it, and in any case the root finders should be able to cope with a certain amount of random-fuzz down at the sharp end. We shouldn't really be trying to iterate down to the last epsilon anyway for that very reason. Do you have a test case?  
  
Consider being in `second_order_root_finder` [here](https://github.com/boostorg/math/blob/c5d36664fd9a15293ae3b319f71e51138c926b7c/include/boost/math/tools/roots.hpp#L581). I then unpack the tuple consisting of `f0`, `f1` and `f2`.  
  
A few lines later, [here](https://github.com/boostorg/math/blob/c5d36664fd9a15293ae3b319f71e51138c926b7c/include/boost/math/tools/roots.hpp#L596), is the exact check on 0 that I mention.  
  
The test case is shown below for a similar `cpp_bin_float`. For `cpp_bin_float`, we break the loop at `f0 == 0`, but my `cpp_double_double` keeps iterating until max-iter is reached.  
  
I suppose I might have some extreme edges around max/subnormal that catch the _fuzz_ I'm talking about. The reason why I never converged in my case was that `tgamma` always returned a value a few epsilon away from the expected convergence value. And I never got to zero/equality. I was in the place of `tgamma` using 113-digit coefficients. Indeed, I need to see exactly _why_ the coefficients for `cpp_double_double` on `tgamma` did not converge as they did for `cpp_bin_float`.  
  
But I was simply wondering if [this line](https://github.com/boostorg/math/blob/c5d36664fd9a15293ae3b319f71e51138c926b7c/include/boost/math/tools/roots.hpp#L596) would be better with a tolerance instead of zero?  
  
The parameter values are  
  
$$  
a~=~\frac{6846611}{17179869184}\text{,}  
$$  
  
having denominator $2^{36}$,  
  
and  
  
$$  
p~=~\frac{13668795}{16777216}\text{,}  
$$  
  
having denominator $2^{24}$,  
  
TEST CASE:  
  
```  
  
  
#include <boost/multiprecision/cpp_bin_float.hpp>  
//#include <boost/multiprecision/cpp_double_fp.hpp>  
#include <boost/math/special_functions/gamma.hpp>  
  
#include <iomanip>  
#include <iostream>  
#include <sstream>  
  
auto main() -> int  
{  
  //  {{ SC_(0.0003985252114944159984588623046875), SC_(0.814723670482635498046875), SC_(0.2831424539072253988416634201709116176591661423194009934428563883171035338046521635771507798074786634e-223), SC_(0.3353627472060982062455262714971853766759653506601897258914838347358670615581935804795450176146751688e-1837) }},   
  
  //using mp_backend_type = boost::multiprecision::cpp_double_fp_backend<double>;  
  using mp_backend_type = boost::multiprecision::cpp_bin_float<106, boost::multiprecision::digit_base_2, void, int, -968, 1024>;  
  
  using mp_type = boost::multiprecision::number<mp_backend_type, boost::multiprecision::et_off>;  
  
  
  //       6846611  
  // a = ----------- ,  
  //     17179869184  
  // having denominator 2^36.  
  
  //     13668795  
  // p = -------- ,  
  //     16777216  
  // having denominator 2^24.  
  
  const mp_type a { "0.0003985252114944159984588623046875" };  
  const mp_type p { "0.814723670482635498046875" };  
  
  {  
    std::stringstream strm { };  
  
    strm << std::setprecision(std::numeric_limits<mp_type>::digits10) << "a: " << a;  
  
    std::cout << strm.str() << std::endl;  
  }  
  
  {  
    std::stringstream strm { };  
  
    strm << std::setprecision(std::numeric_limits<mp_type>::digits10) << "p: " << p;  
  
    std::cout << strm.str() << std::endl;  
  }  
  
  const mp_type gpi { boost::math::gamma_p_inv(a, p) };  
  
  {  
    std::stringstream strm { };  
  
    strm << std::setprecision(std::numeric_limits<mp_type>::digits10) << "gpi: " << gpi;  
  
    std::cout << strm.str() << std::endl;  
  }  
}  
```

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2024-12-28 12:52:26 UTC  
> Updated_at: 2024-12-28 12:53:25 UTC  
> Url: https://github.com/boostorg/math/pull/1231#issuecomment-2564318109  

For the `cpp_double_double` backend, I reach a point in Halley iteration where my `step`-size does not decrease any more. It stays frozen at a particular value.  
  
That's why I stay in the Halley iteration forever untill $200$ (the maximum until throw) steps are reached. Somehow, these test-parameter-values in combination with this novel backend have confounded the subroutine `halley_step::step` which is [here](https://github.com/boostorg/math/blob/c5d36664fd9a15293ae3b319f71e51138c926b7c/include/boost/math/tools/roots.hpp#L375).  
  
It is here, that I keep hitting line 394 and nothing changes.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2024-12-28 16:46:19 UTC  
> Url: https://github.com/boostorg/math/pull/1231#issuecomment-2564377818  

OK there's some screwiness happening here... the iteration is actually flip flopping between small positive and small negative steps.  I think this should be handled by the convergence check here https://github.com/boostorg/math/blob/c5d36664fd9a15293ae3b319f71e51138c926b7c/include/boost/math/tools/roots.hpp#L632 but in the previous calculation of of `convergence` the expression `tools::max_value<T>() * delta2` returns a NaN even though delta2 is tiny (actually the result might be infinite or zero, but never a NaN), so the comparison fails and we end up in the wrong code branch.  This is with the `cpp_double_fp_backend` backend.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2024-12-28 18:15:49 UTC  
> Updated_at: 2024-12-28 18:16:41 UTC  
> Url: https://github.com/boostorg/math/pull/1231#issuecomment-2564394893  

> but in the previous calculation of of convergence the expression `tools::max_value<T>() * delta2` returns a NaN even though delta2 is tiny (actually the result might be infinite or zero, but never a NaN),  
  
Many thanks for this sanity check, John.  
  
> so the comparison fails and we end up in the wrong code branch. This is with the `cpp_double_fp_backend` backend.  
  
The least-well-trusted and newest code is the `cpp_double_fp_backend` implementation. We have, in fact, struggled with inf, NaN, zeros, max/min and the lot.  
  
Based on your input, I can see where that thing goes haywire on a NaN. I do not think that the parameter ranges in this calculation should over/underflow.  
  
So let me shake this thing out a bit more and see what is exactly going haywire. The goal is to leave Math alone since literally every thing passes there. I do, however, really appreciate your sanity check since I had not seen the NaN thing that you picked up.  
  
Did I ever mention, these _specfun_ tests are really good. They really build confidence in your backend impl. These tests alone are worth a lot.  
  
Cc: @mborland

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2024-12-28 18:34:04 UTC  
> Url: https://github.com/boostorg/math/pull/1231#issuecomment-2564398476  

> Did I ever mention, these specfun tests are really good. They really build confidence in your backend impl. These tests alone are worth a lot.  
  
Yeah, it's interesting, they shook out a whole load of bugs in the other multiprecision backends as well when testing: no matter how many tests you think you've devised, just the shear quantity of arithmetic going on finds a whole lot of stuff you would never find otherwise!

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2024-12-29 09:25:22 UTC  
> Url: https://github.com/boostorg/math/pull/1231#issuecomment-2564664150  

>> but in the previous calculation of of convergence the expression tools::max_value<T>() * delta2 returns a NaN even though delta2 is tiny (actually the result might be infinite or zero, but never a NaN),  
  
> Many thanks for this sanity check, John.  
  
Ah OK. John many thanks as usual. Your detailed NaN detection revealed an error in my backend's `eval_multiply` routine.  
  
When performing `tools::max_value<T>() * delta2` my multiplication does a fatal error. The LHS high-part is first multiplied by the so-called _split_ in order to do the two-component multiplication. Now the split has a medium-high value like $2^n$, where $n$ depends on the underlying float-type. OK, so this overflows every single time in my multiplication.  
  
I need to perform an alternate scaling or dig up some other kind of multiplication algorithm when the leading LHS is already high enough to overflow when multiplied by the split, yet the expected multiplication result will remain finite in the end.  
  
I did a quick fix locally and got the entire `igamma_inv` test passing locally. But I made a note for myself to check the validity of my fix in the long term.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2024-12-29 09:28:35 UTC  
> Url: https://github.com/boostorg/math/pull/1231#issuecomment-2564664814  

Clesed as expected. These tests revealed yet another weakness in the fledgling backend code.

---
