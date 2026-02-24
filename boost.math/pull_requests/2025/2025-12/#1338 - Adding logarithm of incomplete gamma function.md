# #1338 Adding logarithm of incomplete gamma function [Closed]

> Username: JacobHass8  
> Created at: 2025-12-12 18:13:32 UTC  
> Updated at: 2025-12-19 13:59:35 UTC  
> Closed at: 2025-12-17 17:50:13 UTC  
> Url: https://github.com/boostorg/math/pull/1338  

First draft of adding logarithm of incomplete gamma function. This still has a number of issues. Closes #1173.

---

## Comment 1

> Username: JacobHass8  
> Created_at: 2025-12-12 18:26:09 UTC  
> Updated_at: 2025-12-12 21:22:44 UTC  
> Url: https://github.com/boostorg/math/pull/1338#issuecomment-3647644824  

As noted above, there are still a number of issues with this PR. Here's a list of issues that I've run into:   
  
1. [logsumexp.hpp](https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/logsumexp.hpp) and [logaddexp.hpp](https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/logaddexp.hpp) don't work for complex numbers. I had to modify `logaddexp.hpp` to work with `std::complex<>` types. However, `logaddexp.hpp` doesn't work with `boost::multiprecision::cpp_complex_50`. The reason is because there is no `std::real` function defined for `boost::multiprecision::cpp_complex_50`. I think `logsumexp.hpp` would be the way to go, but the current template doesn't allow for complex numbers. I'm still new to C++ and I'm not sure how to fix this.   
2. When comparing the results of my function to things like Mathematica, I am sometimes off by a factor of $e^{2 \pi i}$. It's not like this is wrong, but when taking logarithms can/should I always work with the principle branch?   
3. When the logarithm of the incomplete gamma function is ready to implement, where should this go in the file structure? I assume a new function in [gamma.hpp](https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/gamma.hpp)?  
  
@jzmaddock or @mborland do you have any suggestions?

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2025-12-13 12:19:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1338#pullrequestreview-3574356344  

📁 include/boost/math/special_functions/logaddexp.hpp

```diff
  35 |     {
  35 |-         return x1 + log1p(exp(-temp));
  36 |+         return x1 + log(1.0 + exp(-temp));
```

> Username: jzmaddock  
> Created_at: 2025-12-13 12:19:11 UTC  
> Url: https://github.com/boostorg/math/pull/1338#discussion_r2616275332  

This is a really bad change that will kill precision when temp is small.

> Username: JacobHass8  
> Created_at: 2025-12-14 22:34:20 UTC  
> Updated_at: 2025-12-14 22:34:21 UTC  
> Url: https://github.com/boostorg/math/pull/1338#discussion_r2617558038  

I've reverted this for the `logaddexp` function and created a new function for adding complex numbers. I haven't been able to find an equivalent of `log1p` that works with complex numbers. Could I just make a new function `clog1p` which computes the Taylor series of $log(1+x)$ for $|x| < 1$? This seems easy enough that I'm surprised I haven't found an implementation.


---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2025-12-13 12:21:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1338#pullrequestreview-3574356988  

📁 include/boost/math/tools/fraction.hpp

```diff
 114 | template <typename Gen, typename U>
 113 |- BOOST_MATH_GPU_ENABLED inline typename detail::fraction_traits<Gen>::result_type continued_fraction_b_impl(Gen& g, const U& factor, boost::math::uintmax_t& max_terms)
 115 |+ BOOST_MATH_GPU_ENABLED inline typename detail::fraction_traits<Gen>::result_type continued_fraction_b_impl(Gen& g, const U& factor, boost::math::uintmax_t& max_terms, bool logArithmetic)
```

> Username: jzmaddock  
> Created_at: 2025-12-13 12:21:06 UTC  
> Updated_at: 2025-12-13 12:21:07 UTC  
> Url: https://github.com/boostorg/math/pull/1338#discussion_r2616276458  

Since the log and non-log versions basically have completely different code, I see this as two different functions?

> Username: JacobHass8  
> Created_at: 2025-12-14 22:35:31 UTC  
> Url: https://github.com/boostorg/math/pull/1338#discussion_r2617558620  

I've separated this into a new function in `gamma.hpp`. I wasn't sure what the correct implementation should have been.


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2025-12-13 12:22:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1338#pullrequestreview-3574357289  

📁 include/boost/math/tools/fraction.hpp

```diff
 261 | template <typename Gen, typename U>
 239 |- BOOST_MATH_GPU_ENABLED inline typename detail::fraction_traits<Gen>::result_type continued_fraction_a_impl(Gen& g, const U& factor, boost::math::uintmax_t& max_terms)
 262 |+ BOOST_MATH_GPU_ENABLED inline typename detail::fraction_traits<Gen>::result_type continued_fraction_a_impl(Gen& g, const U& factor, boost::math::uintmax_t& max_terms, bool logArithmetic=false)
```

> Username: jzmaddock  
> Created_at: 2025-12-13 12:22:07 UTC  
> Url: https://github.com/boostorg/math/pull/1338#discussion_r2616276914  

Again this looks like two different functions to me.


---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2025-12-13 12:25:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1338#pullrequestreview-3574358814  

📁 include/boost/math/special_functions/logaddexp.hpp

```diff
  33 | 
  33 |-     if (temp > 0)
  34 |+     if (std::real(temp) > 0)
```

> Username: jzmaddock  
> Created_at: 2025-12-13 12:25:31 UTC  
> Url: https://github.com/boostorg/math/pull/1338#discussion_r2616278577  

The trick here, is to add `using std::real` above, and then call `real(temp)`, that way ADL can find `boost::multiprecision::real` in the multiprecision case (likewise for any other type that has their own namespace and is forbidden from putting stuff in `std::`).


---

## Comment 6

> Username: jzmaddock  
> Created_at: 2025-12-13 12:37:14 UTC  
> Url: https://github.com/boostorg/math/pull/1338#issuecomment-3649369987  

Some comments in the code, but to address your other points:  
  
> When comparing the results of my function to things like Mathematica, I am sometimes off by a factor of e 2 π i . It's not like this is wrong, but when taking logarithms can/should I always work with the principle branch?  
  
Um, yes, but if it's not delivering that already you may need some deep math knowledge to figure out why not.  
  
>When the logarithm of the incomplete gamma function is ready to implement, where should this go in the file structure? I assume a new function in [gamma.hpp](https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/gamma.hpp)?  
  
Yes, although the file is getting a bit big ;)  
  
I have a few concerns about doing this in the complex domain:  
  
1) this is the main one: in principle, functions with four degrees of freedom are basically untestable, certainly it's going to need an awful lot of testing.  FYI it took me quite a few months to produce decent tests for 1F1 and work through the issues, granted 1F1 is a freaking monster, but still...  
2) I would expect real valued arguments (which is all that are needed in the stats case) would be the most used, and should probably be optimised and have their own tests.  
3) The original bug report relates to only integer degrees of freedom for the Poisson distribution.  And as noted, that special case has rather trivial code (easy to write and test!) which is worth optimising out too.  
  
But don't let me stop you if you want to smack your head against this particular (and complex!) wall ;)  And probably just extending the continued fraction code to the complex domain is a worthy goal in it's own right.

---

## Comment 7

> Username: JacobHass8  
> Created_at: 2025-12-14 22:52:31 UTC  
> Url: https://github.com/boostorg/math/pull/1338#issuecomment-3652338523  

> Um, yes, but if it's not delivering that already you may need some deep math knowledge to figure out why not.  
  
I think the issue stems from adding/subtracting complex numbers. When returning the log of the incomplete gamma function, I add the prefactor `a * log(z) - z` to the continued fraction.  This is probably causing the returned number to be outside the principle branch since adding/subtracting complex numbers can result in a number outside the principle branch.   
  
Would it be useful to create a function that converts complex numbers to the principle branch?   
  
> I have a few concerns about doing this in the complex domain:  
>   
>     1. this is the main one: in principle, functions with four degrees of freedom are basically untestable, certainly it's going to need an awful lot of testing.  FYI it took me quite a few months to produce decent tests for 1F1 and work through the issues, granted 1F1 is a freaking monster, but still...  
>   
>     2. I would expect real valued arguments (which is all that are needed in the stats case) would be the most used, and should probably be optimised and have their own tests.  
>   
>     3. The original bug report relates to only integer degrees of freedom for the Poisson distribution.  And as noted, that special case has rather trivial code (easy to write and test!) which is worth optimising out too.  
  
Surprisingly, I think working with complex numbers is a lot easier. I tried to revert to only using real numbers but the big issue I ran into was taking the log of negative numbers. There are cases in the continued fraction algorithm where I need to take the log of a negative number. With complex numbers this is easy, but if we only wanted to implement real numbers, this would require catching and changing the algorithm accordingly. I'm not sure if I explained this that well.   
  
I agree that the complex case won't be used often (if at all). I wouldn't be opposed to accepting real valued inputs, converting to complex values for the calculations, and returning the real value (the imaginary component is always close to 0). This would make it much easier to test.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2025-12-15 13:51:08 UTC  
> Url: https://github.com/boostorg/math/pull/1338#issuecomment-3655757703  

>Surprisingly, I think working with complex numbers is a lot easier. I tried to revert to only using real numbers but the big issue I ran into was taking the log of negative numbers. There are cases in the continued fraction algorithm where I need to take the log of a negative number. With complex numbers this is easy, but if we only wanted to implement real numbers, this would require catching and changing the algorithm accordingly. I'm not sure if I explained this that well.  
  
>I agree that the complex case won't be used often (if at all). I wouldn't be opposed to accepting real valued inputs, converting to complex values for the calculations, and returning the real value (the imaginary component is always close to 0). This would make it much easier to test.  
  
Then the issue is this: the continued fraction is only very slowly convergent over some of the domain (especially for `Q(a,x)` and x small.  In fact I found the CF to be flat out unstable for small `x < 1.1`.  Oh, and it's about 2x slower than the other methods most of the time, even worse than that if you're doing complex arithmetic.  So while it's conceptually attractive to say "lets just use method X", in practice that's unlikely to give either a timely or accurate answer for most of the problem domain.  
  
So if it was me.... I would simply take the log of Q(a, x) where the answer is known not to underflow (and we know that function "works"), and where underflow is likely because `x >> a` then take logs of the asymptotic expansion: https://dlmf.nist.gov/8.11#E2.  
  
I had a very quick experiment and came up with this, which needs a lot more testing, but seems to work well:  
  
```  
#include <boost/math/special_functions/gamma.hpp>  
#include <iostream>  
  
//TODO add non-policy version and all the promotion boilerplate, plus some actual error checking...  
  
template <class T, class Policy>  
T log_gamma_q(T a, T x, const Policy& pol)  
{  
   using namespace boost::math;  // temporary until we're in the right namespace  
  
   BOOST_MATH_STD_USING_CORE  
  
   if (((x > 1000) && ((a < x) || (fabs(a - 50) / x < 1))) || ((x > tools::log_max_value<T>() - 10) && (x > a)))  
   {  
      //  
      // Take the logarithmic version of the asymtotic expansion:  
      //  
      return log(detail::incomplete_tgamma_large_x(a, x, pol)) + a * log(x) - x - lgamma(a, pol) - log(x);  
   }  
   //  
   // Can't do better than taking the log of Q, but...  
   //  
   // Figure out whether we need P or Q, since if we calculate Q and it's too close to unity  
   // we will loose precision in the result, selection logic here is extracted from gamma_incomplete_imp_final:  
   //  
   bool need_p = false;  
   if ((x < 0.5) && (T(-0.4) / log(x) < a))  
      need_p = true;  
   else if ((x < 1.1) && (x >= 0.5) && (x * 0.75f < a))  
      need_p = true;  
   else if ((x < a) && (x >= 1.1))  
      need_p = true;  
  
   if (need_p)  
      return log1p(-gamma_p(a, x, pol), pol);  
   return log(gamma_q(a, x, pol));  
}  
  
  
int main(int argc, char* argv[])  
{  
   boost::math::policies::policy<> pol;  
  
   std::cout << log_gamma_q(0.25, 0.125, pol) << std::endl;  
   std::cout << log_gamma_q(0.25, 0.25, pol) << std::endl;  
  
   std::cout << log_gamma_q(0.75, 0.999, pol) << std::endl;  
   std::cout << log_gamma_q(0.75, 1.001, pol) << std::endl;  
  
   std::cout << log_gamma_q(2.5, 2.25, pol) << std::endl;  
   std::cout << log_gamma_q(2.5, 2.525, pol) << std::endl;  
  
   std::cout << log_gamma_q(2.5, 710.0, pol) << std::endl;  
   std::cout << log_gamma_q(20.5, 710.0, pol) << std::endl;  
   std::cout << log_gamma_q(200.5, 710.0, pol) << std::endl;  
   std::cout << log_gamma_q(710.25, 710.0, pol) << std::endl;  
   std::cout << log_gamma_q(710.25, 1000.25, pol) << std::endl;  
   std::cout << log_gamma_q(1000.125 + 50, 1000.25, pol) << std::endl;  
   std::cout << log_gamma_q(1000.26 + 50, 1000.25, pol) << std::endl;  
  
   return 0;  
}  
```

---

## Comment 9

> Username: JacobHass8  
> Created_at: 2025-12-15 18:19:03 UTC  
> Url: https://github.com/boostorg/math/pull/1338#issuecomment-3656991873  

> Then the issue is this: the continued fraction is only very slowly convergent over some of the domain (especially for `Q(a,x)` and x small. In fact I found the CF to be flat out unstable for small `x < 1.1`. Oh, and it's about 2x slower than the other methods most of the time, even worse than that if you're doing complex arithmetic. So while it's conceptually attractive to say "lets just use method X", in practice that's unlikely to give either a timely or accurate answer for most of the problem domain.  
  
I didn't realize the CF converges so slowly. I guess that makes sense since it's a sort of iterative method.   
  
> So if it was me.... I would simply take the log of Q(a, x) where the answer is known not to underflow (and we know that function "works"), and where underflow is likely because `x >> a` then take logs of the asymptotic expansion: https://dlmf.nist.gov/8.11#E2.  
  
That makes a lot more sense. I should have thought of this sooner. Should I delete this PR and start over with your code above? I'm not sure if there's any applications for log arithmetic continued fractions.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2025-12-17 17:50:13 UTC  
> Url: https://github.com/boostorg/math/pull/1338#issuecomment-3666494610  

> I didn't realize the CF converges so slowly. I guess that makes sense since it's a sort of iterative method.  
  
I *love* continued fractions, but as with series approximations, they always have zones of good/bad convergence: which is only to be expected as for every CF there is a 1:1 mapping onto an equivalent series.  
  
> That makes a lot more sense. I should have thought of this sooner. Should I delete this PR and start over with your code above? I'm not sure if there's any applications for log arithmetic continued fractions.  
  
In that case yes, let's start with a fresh PR as it keeps the version history a bit cleaner?

---

## Comment 11

> Username: JacobHass8  
> Created_at: 2025-12-18 00:39:47 UTC  
> Url: https://github.com/boostorg/math/pull/1338#issuecomment-3667735884  

> I had a very quick experiment and came up with this, which needs a lot more testing, but seems to work well:  
  
I've done some quick tests comparing your function to Mathematica and they match up very well! It was accurate for almost all regimes I tried like $x \gg a$ and $x \ll a$.    
  
> //TODO add non-policy version and all the promotion boilerplate, plus some actual error checking...  
  
The non-policy version seemed pretty easy. I took a look at other functions in `gamma.hpp` and they just passed a default policy, `policies::policy<>()`. Is that the correct approach?   
  
I'm not sure what you mean by "promotion boilerplate" - this is my first contribution to boost. Is this in regards to the return argument type `tools::promote_args<T>::type` which I'm seeing in most functions in `gamma.hpp`?

---

## Comment 12

> Username: dschmitz89  
> Created_at: 2025-12-18 08:15:14 UTC  
> Url: https://github.com/boostorg/math/pull/1338#issuecomment-3668965190  

@JacobHass8 Thanks for your help in enhancing boost on behalf of issues raised in SciPy, much appreciated!  
  
I thought I would ask if you would be interested in helping out with #1305. Finishing that off will enable SciPy to drop one of the ancient libraries that are unfortunately still in use, cdflib. It could also likely be easier than this issue since we can basically copy paste the approach from other distributions (it is basically a straight forward usage of a derivative-free rootfinder).

---

## Comment 13

> Username: JacobHass8  
> Created_at: 2025-12-18 18:14:27 UTC  
> Url: https://github.com/boostorg/math/pull/1338#issuecomment-3671522768  

> I thought I would ask if you would be interested in helping out with #1305. Finishing that off will enable SciPy to drop one of the ancient libraries that are unfortunately still in use, cdflib. It could also likely be easier than this issue since we can basically copy paste the approach from other distributions (it is basically a straight forward usage of a derivative-free rootfinder).  
  
Sure thing! Where exactly do you think I should start? It looks like there are a number of open issues connected to #1305.

---

## Comment 14

> Username: dschmitz89  
> Created_at: 2025-12-19 13:59:35 UTC  
> Url: https://github.com/boostorg/math/pull/1338#issuecomment-3675187807  

@JacobHass8 Awesome!  
  
We need to add inverses to the CDFs of the following distributions in boost (see https://github.com/scipy/scipy/issues/21966): `fisher_f`, `student_t`, `noncentral_t` and `noncentral_f`  
 For the noncentral chi squared distribution, such inverses are for example already implemented: see [here](https://github.com/boostorg/math/blob/f6be8e863a2c233f6877bc12fd27ae3c445b4b20/include/boost/math/distributions/non_central_chi_squared.hpp#L627).  
  
Over at SciPy, I had started working on the inverse of the noncentral F CDF with respect to the noncentrality in https://github.com/scipy/scipy/pull/23308. You could look into implementing this in boost itself using the noncentral chi squared code as a template. It should be very similar for other distributions. In case that you do not have a CUDA capable GPU on your own machine, it should be fine to leave out the GPU specific macro stuff for a first PR.  
  
Feel free to ping me again in case of questions.

---
