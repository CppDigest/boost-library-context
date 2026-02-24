# #1125 Improve Heuman Lambda precision: [Merged]

> Username: jzmaddock  
> Created at: 2024-04-25 18:53:37 UTC  
> Updated at: 2024-05-03 10:25:45 UTC  
> Merged at: 2024-05-03 10:25:45 UTC  
> Closed at: 2024-05-03 10:25:45 UTC  
> Url: https://github.com/boostorg/math/pull/1125  

Make sure we pass 1 - k^2 down through the call stack otherwise we get cancellation errors.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2024-04-26 11:14:58 UTC  
> Url: https://github.com/boostorg/math/pull/1125#issuecomment-2079185593  

@mborland the clang/Mac errors are coming down, but there's still a few left to investigate, starting with the binomial failures, can I get you to run the following?  The quantile is returning zero in this case which is very strange:  
  
```  
#define BOOST_MATH_INSTRUMENT  
  
#include <boost/math/distributions.hpp>  
#include <iostream>  
  
namespace boost::math::detail  
{  
   template <class RealType, class Policy>  
   RealType quantile_imp_local(const binomial_distribution<RealType, Policy>& dist, const RealType& p, const RealType& q, bool comp)  
   { // Quantile or Percent Point Binomial function.  
     // Return the number of expected successes k,  
     // for a given probability p.  
     //  
     // Error checks:  
      BOOST_MATH_STD_USING  // ADL of std names  
         RealType result = 0;  
      RealType trials = dist.trials();  
      RealType success_fraction = dist.success_fraction();  
      if (false == binomial_detail::check_dist_and_prob(  
         "boost::math::quantile(binomial_distribution<%1%> const&, %1%)",  
         trials,  
         success_fraction,  
         p,  
         &result, Policy()))  
      {  
         return result;  
      }  
  
      // Special cases:  
      //  
      if (p == 0)  
      {  // There may actually be no answer to this question,  
         // since the probability of zero successes may be non-zero,  
         // but zero is the best we can do:  
         BOOST_MATH_INSTRUMENT_VARIABLE(p);  
         return 0;  
      }  
      if (p == 1 || success_fraction == 1)  
      {  // Probability of n or fewer successes is always one,  
         // so n is the most sensible answer here:  
         BOOST_MATH_INSTRUMENT_VARIABLE(p);  
         return trials;  
      }  
      if (p <= pow(1 - success_fraction, trials))  
      { // p <= pdf(dist, 0) == cdf(dist, 0)  
         BOOST_MATH_INSTRUMENT_VARIABLE(p);  
         return 0; // So the only reasonable result is zero.  
      } // And root finder would fail otherwise.  
  
      // Solve for quantile numerically:  
      //  
      RealType guess = binomial_detail::inverse_binomial_cornish_fisher(trials, success_fraction, p, q, Policy());  
      BOOST_MATH_INSTRUMENT_VARIABLE(guess);  
      RealType factor = 8;  
      if (trials > 100)  
         factor = 1.01f; // guess is pretty accurate  
      else if ((trials > 10) && (trials - 1 > guess) && (guess > 3))  
         factor = 1.15f; // less accurate but OK.  
      else if (trials < 10)  
      {  
         // pretty inaccurate guess in this area:  
         if (guess > trials / 64)  
         {  
            guess = trials / 4;  
            factor = 2;  
         }  
         else  
            guess = trials / 1024;  
      }  
      else  
         factor = 2; // trials largish, but in far tails.  
  
      typedef typename Policy::discrete_quantile_type discrete_quantile_type;  
      std::uintmax_t max_iter = policies::get_max_root_iterations<Policy>();  
      result = detail::inverse_discrete_quantile(  
         dist,  
         comp ? q : p,  
         comp,  
         guess,  
         factor,  
         RealType(1),  
         discrete_quantile_type(),  
         max_iter);  
      return result;  
   } // quantile  
  
}  
  
int main()  
{  
   using namespace boost::math::policies;  
   using namespace boost::math;  
     
   typedef policy<discrete_quantile<integer_round_outwards> > Policy;  
   binomial_distribution<double, Policy> dist(9079765771874083840, 0.561815);  
   std::cout << boost::math::detail::quantile_imp_local(dist, 0.0365346, 1- 0.0365346, false) << std::endl;  
  
   return 0;  
}  
```

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-04-26 11:15:54 UTC  
> Url: https://github.com/boostorg/math/pull/1125#issuecomment-2079186855  

The other main errors are from autodiff, I can't tell from the output if these are small tolerance failures, or something more serious?

---

## Comment 3

> Username: mborland  
> Created_at: 2024-04-26 14:43:53 UTC  
> Url: https://github.com/boostorg/math/pull/1125#issuecomment-2079536121  

Clang 18:  
  
```  
lgamma_imp called with e N5boost4math7lanczos12lanczos13m53E  
scrap.cpp:53 guess = 5101148604445670400  
../../../boost/math/special_functions/beta.hpp:365 b1 = 781823564564887552  
../../../boost/math/special_functions/beta.hpp:366 b2 = 0.43818500000000004668265773943858221  
../../../boost/math/special_functions/beta.hpp:367 l1 = 41.200405489238789868977619335055351  
../../../boost/math/special_functions/beta.hpp:368 l2 = -7491842611905637376  
../../../boost/math/special_functions/beta.hpp:411 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148604445670400  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617167428413440  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 1.5273896380250717763985214018598168e-69  
../../../boost/math/special_functions/beta.hpp:421 result = 1.5273896380250717763985214018598168e-69  
../../../boost/math/special_functions/beta.hpp:831 fract = 3275226113.27406978607177734375  
../../../boost/math/special_functions/beta.hpp:832 result = 1.5273896380250717763985214018598168e-69  
../../../boost/math/special_functions/beta.hpp:1477 fract = 4.6634631784192189631505919945206929e-79  
../../../boost/math/special_functions/beta.hpp:1107 a = 1  
../../../boost/math/special_functions/beta.hpp:1108 b = 9079765771874083840  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/distributions/detail/inv_discrete_quantile.hpp:226 a = 0 b = 0 fa = -0.036534600000000000408206801694177557 fb = -0.036534600000000000408206801694177557 count = 198  
../../../boost/math/special_functions/beta.hpp:1107 a = 1  
../../../boost/math/special_functions/beta.hpp:1108 b = 9079765771874083840  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
0  
```  
  
GCC-13  
```  
lgamma_imp called with e N5boost4math7lanczos12lanczos13m53E  
scrap.cpp:53 guess = 5101148604445670400  
../../../boost/math/special_functions/beta.hpp:365 b1 = 781823564564887552  
../../../boost/math/special_functions/beta.hpp:366 b2 = 0.43818500000000004668265773943858221  
../../../boost/math/special_functions/beta.hpp:367 l1 = 41.200405489238789868977619335055351  
../../../boost/math/special_functions/beta.hpp:368 l2 = -7491842611905637376  
../../../boost/math/special_functions/beta.hpp:411 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148604445670400  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617167428413440  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 30011286371.700000762939453125  
../../../boost/math/special_functions/beta.hpp:421 result = 30011286371.700000762939453125  
../../../boost/math/special_functions/beta.hpp:831 fract = 3275226176.913488864898681640625  
../../../boost/math/special_functions/beta.hpp:832 result = 30011286371.700000762939453125  
../../../boost/math/special_functions/beta.hpp:1477 fract = 9.1631187437510259741202389704994857  
../../../boost/math/special_functions/beta.hpp:1107 a = 5050642230309255168  
../../../boost/math/special_functions/beta.hpp:1108 b = 4029123541564828672  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/distributions/detail/inv_discrete_quantile.hpp:253 a = 5050642230309255168 b = 5101148604445670400 fa = -0.036534600000000000408206801694177557 fb = 9.1265841437510264455568176344968379 count = 198  
../../../boost/math/special_functions/beta.hpp:1107 a = 5050843606088411136  
../../../boost/math/special_functions/beta.hpp:1108 b = 4028922165785672704  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:351  a = 5050843606088411136 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5063595008354653184  
../../../boost/math/special_functions/beta.hpp:1108 b = 4016170763519430656  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:363  a = 5063595008354653184 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5069545918294018048  
../../../boost/math/special_functions/beta.hpp:1108 b = 4010219853580065792  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5069545918294018048 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5071879966497594368  
../../../boost/math/special_functions/beta.hpp:1108 b = 4007885805376489472  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5071879966497594368 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5072113362573729792  
../../../boost/math/special_functions/beta.hpp:1108 b = 4007652409300354048  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5072113362573729792 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -0.0057244859876724651578938640739124821  
../../../boost/math/special_functions/beta.hpp:1107 a = 5086630983509699584  
../../../boost/math/special_functions/beta.hpp:1108 b = 3993134788364384256  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5086630983509699584 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5087680435139004416  
../../../boost/math/special_functions/beta.hpp:1108 b = 3992085336735079424  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5087680435139004416 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5089098579332574208  
../../../boost/math/special_functions/beta.hpp:1108 b = 3990667192541509632  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5089098579332574208 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5089194669508813824  
../../../boost/math/special_functions/beta.hpp:1108 b = 3990571102365270016  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5089194669508813824 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -0.002348885376398716657436738231012896  
../../../boost/math/special_functions/beta.hpp:1107 a = 5095171636977242112  
../../../boost/math/special_functions/beta.hpp:1108 b = 3984594134896841728  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5095171636977242112 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5095603700762052608  
../../../boost/math/special_functions/beta.hpp:1108 b = 3984162071112031232  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5095603700762052608 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5096187556873732096  
../../../boost/math/special_functions/beta.hpp:1108 b = 3983578215000351744  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5096187556873732096 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5096227117615809536  
../../../boost/math/special_functions/beta.hpp:1108 b = 3983538654258274304  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5096227117615809536 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -0.00096571183274937733281156893028196464  
../../../boost/math/special_functions/beta.hpp:1107 a = 5098687861030739968  
../../../boost/math/special_functions/beta.hpp:1108 b = 3981077910843343872  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5098687861030739968 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5098865743563800576  
../../../boost/math/special_functions/beta.hpp:1108 b = 3980900028310283264  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5098865743563800576 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5099106119655801856  
../../../boost/math/special_functions/beta.hpp:1108 b = 3980659652218281984  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5099106119655801856 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5099122406984798208  
../../../boost/math/special_functions/beta.hpp:1108 b = 3980643364889285632  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5099122406984798208 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -0.00039736199666351890592147499958741719  
../../../boost/math/special_functions/beta.hpp:1107 a = 5100135505715233792  
../../../boost/math/special_functions/beta.hpp:1108 b = 3979630266158850048  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5100135505715233792 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5100208740725156864  
../../../boost/math/special_functions/beta.hpp:1108 b = 3979557031148926976  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5100208740725156864 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5100307704604184576  
../../../boost/math/special_functions/beta.hpp:1108 b = 3979458067269899264  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5100307704604184576 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5100314410168132608  
../../../boost/math/special_functions/beta.hpp:1108 b = 3979451361705951232  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5100314410168132608 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -0.00016355742223924361628678569857697767  
../../../boost/math/special_functions/beta.hpp:1107 a = 5100731507306901504  
../../../boost/math/special_functions/beta.hpp:1108 b = 3979034264567182336  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5100731507306901504 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5100761658477935616  
../../../boost/math/special_functions/beta.hpp:1108 b = 3979004113396148224  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5100761658477935616 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5100802402335906816  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978963369538177024  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5100802402335906816 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5100805163045650432  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978960608828433408  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5100805163045650432 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -6.7330821123518050938874801580880103e-05  
../../../boost/math/special_functions/beta.hpp:1107 a = 5100976883745659904  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978788888128423936  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5100976883745659904 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5100989297113469952  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978776474760613888  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5100989297113469952 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101006071536408576  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978759700337675264  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101006071536408576 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101007208132577280  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978758563741506560  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101007208132577280 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -2.771929294016027144238745183457695e-05  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101077906289123328  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978687865584960512  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5101077906289123328 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101083016926458880  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978682754947624960  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5101083016926458880 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101089923029094400  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978675848844989440  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101089923029094400 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101090390970663936  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978675380903419904  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101090390970663936 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -1.1411966960727158056630205218429097e-05  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101119497708167168  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978646274165916672  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5101119497708167168 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101121601779710976  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978644170094372864  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5101121601779710976 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101124445052101632  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978641326821982208  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101124445052101632 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101124637705673728  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978641134168410112  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101124637705673728 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -4.6983247220611905895426216317467549e-06  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101136621075671040  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978629150798412800  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5101136621075671040 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101137487331046400  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978628284543037440  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5101137487331046400 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101138657918613504  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978627113955470336  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101138657918613504 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101138737234924544  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978627034639159296  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101138737234924544 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -1.9343153074445741314453788700644665e-06  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101143670840297472  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978622101033786368  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5101143670840297472 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101144027481390080  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978621744392693760  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5101144027481390080 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101144509417366528  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978621262456717312  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101144509417366528 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101144542072235008  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978621229801848832  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101144542072235008 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -7.963650903955260647182757931250574e-07  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101146573258952704  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978619198615131136  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5101146573258952704 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101146720089636864  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978619051784446976  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5101146720089636864 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101146918504766464  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978618853369317376  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101146918504766464 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101146931948917760  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978618839925166080  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101146931948917760 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -3.2786680601984041470990834110654788e-07  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101147768197294080  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978618003676789760  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5101147768197294080 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101147828648122368  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617943225961472  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5101147828648122368 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101147910336491520  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617861537592320  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101147910336491520 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101147915871506432  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617856002577408  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101147915871506432 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -1.3498415931080884753971043700515597e-07  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148260158587904  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617511715495936  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5101148260158587904 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148285046456320  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617486827627520  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5101148285046456320 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148318677912576  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617453196171264  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101148318677912576 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148320956702720  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617450917381120  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101148320956702720 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -5.5573558896800047050508665090126637e-08  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148462701186048  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617309172897792  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5101148462701186048 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148472947629056  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617298926454784  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5101148472947629056 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148486793845760  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617285080238080  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101148486793845760 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148487732033536  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617284142050304  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101148487732033536 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -2.2879874232379144665427733902618446e-08  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148546088851456  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617225785232384  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0  
../../../boost/math/special_functions/beta.hpp:421 result = 0  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5101148546088851456 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148550307356672  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617221566727168  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 3.5991835720604821018961199545040315e-303  
../../../boost/math/special_functions/beta.hpp:421 result = 3.5991835720604821018961199545040315e-303  
../../../boost/math/special_functions/beta.hpp:831 fract = 56857367402.96071624755859375  
../../../boost/math/special_functions/beta.hpp:832 result = 3.5991835720604821018961199545040315e-303  
../../../boost/math/special_functions/beta.hpp:1477 fract = 6.3301973632411252557007564550199742e-314  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5101148550307356672 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148556007904256  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617215866179584  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 2.1375220657258239405396915109496909e-243  
../../../boost/math/special_functions/beta.hpp:421 result = 2.1375220657258239405396915109496909e-243  
../../../boost/math/special_functions/beta.hpp:831 fract = 51161186796.80525970458984375  
../../../boost/math/special_functions/beta.hpp:832 result = 2.1375220657258239405396915109496909e-243  
../../../boost/math/special_functions/beta.hpp:1477 fract = 4.1780150140287607176800658849522079e-254  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101148556007904256 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148556394160128  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617215479923712  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 1.417864810418413978011038081185384e-239  
../../../boost/math/special_functions/beta.hpp:421 result = 1.417864810418413978011038081185384e-239  
../../../boost/math/special_functions/beta.hpp:831 fract = 50775262146.71552276611328125  
../../../boost/math/special_functions/beta.hpp:832 result = 1.417864810418413978011038081185384e-239  
../../../boost/math/special_functions/beta.hpp:1477 fract = 2.7924322799584616691351087239642698e-250  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101148556394160128 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -9.4197431650502809159715726303185868e-09  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148580419914752  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617191454169088  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 7.7842819837858321487204417920074191e-59  
../../../boost/math/special_functions/beta.hpp:421 result = 7.7842819837858321487204417920074191e-59  
../../../boost/math/special_functions/beta.hpp:831 fract = 26788706371.4603424072265625  
../../../boost/math/special_functions/beta.hpp:832 result = 7.7842819837858321487204417920074191e-59  
../../../boost/math/special_functions/beta.hpp:1477 fract = 2.9058073487560811813804166131329789e-69  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5101148580419914752 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148582156691456  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617189717392384  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 4.0722038353280113352831046627693353e-50  
../../../boost/math/special_functions/beta.hpp:421 result = 4.0722038353280113352831046627693353e-50  
../../../boost/math/special_functions/beta.hpp:831 fract = 25057637815.965900421142578125  
../../../boost/math/special_functions/beta.hpp:832 result = 4.0722038353280113352831046627693353e-50  
../../../boost/math/special_functions/beta.hpp:1477 fract = 1.6251347653901108945969638609598819e-60  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5101148582156691456 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:407 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148584503630848  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617187370452992  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 2.886498320516494139092037310403172e-39  
../../../boost/math/special_functions/beta.hpp:421 result = 2.886498320516494139092037310403172e-39  
../../../boost/math/special_functions/beta.hpp:831 fract = 22719771577.669521331787109375  
../../../boost/math/special_functions/beta.hpp:832 result = 2.886498320516494139092037310403172e-39  
../../../boost/math/special_functions/beta.hpp:1477 fract = 1.2704785832237564050331855612529521e-49  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101148584503630848 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148584662653952  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617187211429888  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 1.4350454290852021348897694510633379e-38  
../../../boost/math/special_functions/beta.hpp:421 result = 1.4350454290852021348897694510633379e-38  
../../../boost/math/special_functions/beta.hpp:831 fract = 22561430170.255115509033203125  
../../../boost/math/special_functions/beta.hpp:832 result = 1.4350454290852021348897694510633379e-38  
../../../boost/math/special_functions/beta.hpp:1477 fract = 6.3606137476921090546863383149348203e-49  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101148584662653952 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -3.8781492237808003529786550250997579e-09  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148594554161152  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617177319922688  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 6.6391139377423090774978098327352427e-05  
../../../boost/math/special_functions/beta.hpp:421 result = 6.6391139377423090774978098327352427e-05  
../../../boost/math/special_functions/beta.hpp:831 fract = 12744375760.643070220947265625  
../../../boost/math/special_functions/beta.hpp:832 result = 6.6391139377423090774978098327352427e-05  
../../../boost/math/special_functions/beta.hpp:1477 fract = 5.2094461607488768755388791777445188e-15  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5101148594554161152 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:385 Can't take cubic step!!!!  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148595269199872  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617176604883968  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0.0033032206128091484188813442557375311  
../../../boost/math/special_functions/beta.hpp:421 result = 0.0033032206128091484188813442557375311  
../../../boost/math/special_functions/beta.hpp:831 fract = 12039199372.4472942352294921875  
../../../boost/math/special_functions/beta.hpp:832 result = 0.0033032206128091484188813442557375311  
../../../boost/math/special_functions/beta.hpp:1477 fract = 2.7437211650210248791215925288151683e-13  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5101148595269199872 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:262  a = 5101148595269199872 b = 5101148604445669376 d = 5101148594554161152 e = 5101148584662653952 fa = -0.036534599999725629604352405976896989 fb = 9.1265841437510264455568176344968379 fd = -0.036534599999994789298884967365665943 fe = -0.036534600000000000408206801694177557  
../../../boost/math/tools/toms748_solve.hpp:270 q11 = 69348431712014187888640 q21 = -9852069436.1302089691162109375 q31 = 36587831.29564560949802398681640625 d21 = 39438787.869790621101856231689453125 d31 = -9139881672.70435333251953125  
../../../boost/math/tools/toms748_solve.hpp:278 q22 = 69071930088049749262336 q32 = 96669358711562862592 d32 = 96669358712275042304 q33 = -9184590799101364872097150911119360 c = -9184590799101263415004745508585472  
../../../boost/math/tools/toms748_solve.hpp:286 Out of bounds interpolation, falling back to quadratic interpolation. c = 5101148596235445248  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148596235445248  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617175638638592  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0.45089461473554504067351444973610342  
../../../boost/math/special_functions/beta.hpp:421 result = 0.45089461473554504067351444973610342  
../../../boost/math/special_functions/beta.hpp:831 fract = 11088154785.2916965484619140625  
../../../boost/math/special_functions/beta.hpp:832 result = 0.45089461473554504067351444973610342  
../../../boost/math/special_functions/beta.hpp:1477 fract = 4.0664531066399910622510220281817551e-11  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101148596235445248 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148596300915712  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617175573168128  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 0.61970436969138564808901037395116873  
../../../boost/math/special_functions/beta.hpp:421 result = 0.61970436969138564808901037395116873  
../../../boost/math/special_functions/beta.hpp:831 fract = 11023803535.10449981689453125  
../../../boost/math/special_functions/beta.hpp:832 result = 0.61970436969138564808901037395116873  
../../../boost/math/special_functions/beta.hpp:1477 fract = 5.6215113750710650844646164836629137e-11  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101148596300915712 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -1.5966509326754657185097577325660553e-09  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148600373293056  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617171500790784  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 5569844.9609271259978413581848144531  
../../../boost/math/special_functions/beta.hpp:421 result = 5569844.9609271259978413581848144531  
../../../boost/math/special_functions/beta.hpp:831 fract = 7056677326.802845001220703125  
../../../boost/math/special_functions/beta.hpp:832 result = 5569844.9609271259978413581848144531  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.00078930135288623787422068733832247744  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5101148600373293056 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:262  a = 5101148600373293056 b = 5101148604445669376 d = 5101148596300915712 e = 5101148596235445248 fa = -0.035745298647113760148741334887745325 fb = 9.1265841437510264455568176344968379 fd = -0.036534599943784884512609778539626859 fe = -0.036534599959335466168308670376063674  
../../../boost/math/tools/toms748_solve.hpp:270 q11 = 153816575053794592 q21 = -8112279424.05198192596435546875 q31 = 15887696.319696312770247459411621094 d21 = 32474239.948016863316297531127929688 d31 = -4056488623.680303096771240234375  
../../../boost/math/tools/toms748_solve.hpp:278 q22 = 153203287428667872 q32 = 183675681290.447265625 d32 = 187731472090.818939208984375 q33 = -6938149560305782784 c = -1837000776240920576  
../../../boost/math/tools/toms748_solve.hpp:286 Out of bounds interpolation, falling back to quadratic interpolation. c = 5101148600419585024  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148600419585024  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617171454498816  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 6402738.8195634754374623298645019531  
../../../boost/math/special_functions/beta.hpp:421 result = 6402738.8195634754374623298645019531  
../../../boost/math/special_functions/beta.hpp:831 fract = 7012181655.02098369598388671875  
../../../boost/math/special_functions/beta.hpp:832 result = 6402738.8195634754374623298645019531  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.00091308798524620019779890478517359043  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5101148600419585024 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:262  a = 5101148600419585024 b = 5101148604445669376 d = 5101148600373293056 e = 5101148596300915712 fa = -0.035621512014753803354594197116966825 fb = 9.1265841437510264455568176344968379 fd = -0.035745298647113760148741334887745325 fe = -0.036534599943784884512609778539626859  
../../../boost/math/tools/toms748_solve.hpp:270 q11 = 184426840522.059112548828125 q21 = -4056488623.680303096771240234375 q31 = 15652913.44742180407047271728515625 d21 = 15887696.319696312770247459411621094 d31 = -4010431438.552578449249267578125  
../../../boost/math/tools/toms748_solve.hpp:278 q22 = 183675681290.447296142578125 q32 = 13253665134.230625152587890625 d32 = 13299722319.358348846435546875 q33 = -6646730371251.30859375 c = 5101141966958531584  
../../../boost/math/tools/toms748_solve.hpp:286 Out of bounds interpolation, falling back to quadratic interpolation. c = 5101148600941625344  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148600941625344  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617170932458496  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 28844251.132246859371662139892578125  
../../../boost/math/special_functions/beta.hpp:421 result = 28844251.132246859371662139892578125  
../../../boost/math/special_functions/beta.hpp:831 fract = 6511854857.31957149505615234375  
../../../boost/math/special_functions/beta.hpp:832 result = 28844251.132246859371662139892578125  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.0044294984707505926160853171325015865  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101148600941625344 b = 5101148604445669376  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148600966192128  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617170907891712  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 30868586.846122749149799346923828125  
../../../boost/math/special_functions/beta.hpp:421 result = 30868586.846122749149799346923828125  
../../../boost/math/special_functions/beta.hpp:831 fract = 6488380425.51475620269775390625  
../../../boost/math/special_functions/beta.hpp:832 result = 30868586.846122749149799346923828125  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.0047575180278788577192816688921084278  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101148600966192128 b = 5101148604445669376  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -6.8209682175127448277064924362441019e-10  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148602705930240  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617169168153600  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 1894233777.4496743679046630859375  
../../../boost/math/special_functions/beta.hpp:421 result = 1894233777.4496743679046630859375  
../../../boost/math/special_functions/beta.hpp:831 fract = 4847443840.37121868133544921875  
../../../boost/math/special_functions/beta.hpp:832 result = 1894233777.4496743679046630859375  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.3907696179322034946146402489830507  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5101148600966192128 b = 5101148602705930240  
../../../boost/math/tools/toms748_solve.hpp:262  a = 5101148600966192128 b = 5101148602705930240 d = 5101148604445669376 e = 5101148600941625344 fa = -0.031777081972121146158372084755683318 fb = 0.35423501793220346645085783165995963 fd = 9.1265841437510264455568176344968379 fe = -0.032105101529249405190036270596465329  
../../../boost/math/tools/toms748_solve.hpp:270 q11 = -3491760867.193606853485107421875 q21 = -70252165.6627285778522491455078125 q31 = 143217791.900175392627716064453125 d21 = -1809991301.662728786468505859375 d31 = -1596520320.0998246669769287109375  
../../../boost/math/tools/toms748_solve.hpp:278 q22 = -1542013480.3346636295318603515625 q32 = 5295745.2823287229984998703002929688 d32 = -1520972409.154767513275146484375 q33 = 2038365789.8890335559844970703125 c = 5101148603153071104  
../../../boost/math/tools/toms748_solve.hpp:286 Out of bounds interpolation, falling back to quadratic interpolation. c = 5101148602560163840  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148602560163840  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617169313920000  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 1413196811.9149954319000244140625  
../../../boost/math/special_functions/beta.hpp:421 result = 1413196811.9149954319000244140625  
../../../boost/math/special_functions/beta.hpp:831 fract = 4982945955.64434146881103515625  
../../../boost/math/special_functions/beta.hpp:832 result = 1413196811.9149954319000244140625  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.28360669059920717849365701113129035  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5101148600966192128 b = 5101148602560163840  
../../../boost/math/tools/toms748_solve.hpp:262  a = 5101148600966192128 b = 5101148602560163840 d = 5101148602705930240 e = 5101148604445669376 fa = -0.031777081972121146158372084755683318 fb = 0.24707209059920717808545020943711279 fd = 0.35423501793220346645085783165995963 fe = 9.1265841437510264455568176344968379  
../../../boost/math/tools/toms748_solve.hpp:270 q11 = -70252165.6627285778522491455078125 q21 = -336075264.86476480960845947265625 q31 = 181645759.5566642284393310546875 d21 = -481841664.86476480960845947265625 d31 = -1412325952.44333553314208984375  
../../../boost/math/tools/toms748_solve.hpp:278 q22 = -11452462.413228182122111320495605469 q32 = 88598534.42473097145557403564453125 d32 = -987652153.15383994579315185546875 q33 = 3387153.7526490520685911178588867188 c = 5101148601239823360  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148601239823360  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617170634260480  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 64520608.154548116028308868408203125  
../../../boost/math/special_functions/beta.hpp:421 result = 64520608.154548116028308868408203125  
../../../boost/math/special_functions/beta.hpp:831 fract = 6227387234.75207805633544921875  
../../../boost/math/special_functions/beta.hpp:832 result = 64520608.154548116028308868408203125  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.010360783057538059531710317173747171  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101148601239823360 b = 5101148602560163840  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148601492769792  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617170381314048  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 123804596.2410869300365447998046875  
../../../boost/math/special_functions/beta.hpp:421 result = 123804596.2410869300365447998046875  
../../../boost/math/special_functions/beta.hpp:831 fract = 5986945002.32593441009521484375  
../../../boost/math/special_functions/beta.hpp:832 result = 123804596.2410869300365447998046875  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.020679093626714245557352001014805865  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101148601492769792 b = 5101148602560163840  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -2.0924582508489247508358465986688613e-10  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148602026467328  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617169847616512  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 445800392.73125970363616943359375  
../../../boost/math/special_functions/beta.hpp:421 result = 445800392.73125970363616943359375  
../../../boost/math/special_functions/beta.hpp:831 fract = 5482579317.38701915740966796875  
../../../boost/math/special_functions/beta.hpp:832 result = 445800392.73125970363616943359375  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.081312164753820068141543231376999756  
../../../boost/math/tools/toms748_solve.hpp:461 Not converging: Taking a bisection!!!!  
../../../boost/math/tools/toms748_solve.hpp:462  a = 5101148601492769792 b = 5101148602026467328  
../../../boost/math/tools/toms748_solve.hpp:262  a = 5101148601492769792 b = 5101148602026467328 d = 5101148602560163840 e = 5101148601239823360 fa = -0.015855506373285754850854800679371692 fb = 0.0447775647538200677333364296828222 fd = 0.24707209059920717808545020943711279 fe = -0.026173816942461940876496484520430386  
../../../boost/math/tools/toms748_solve.hpp:270 q11 = -1193867039.514995574951171875 q21 = -118132856.16652214527130126953125 q31 = 139561538.3841570317745208740234375 d21 = -651829368.16652214527130126953125 d31 = -394135997.61584293842315673828125  
../../../boost/math/tools/toms748_solve.hpp:278 q22 = -342081103.25056517124176025390625 q32 = 16644010.05708968639373779296875 d32 = -259359131.3922311365604400634765625 q33 = 127113711.4179049432277679443359375 c = 5101148601776089088  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148601776089088  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617170097994752  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 248314084.719857037067413330078125  
../../../boost/math/special_functions/beta.hpp:421 result = 248314084.719857037067413330078125  
../../../boost/math/special_functions/beta.hpp:831 fract = 5718664958.53714466094970703125  
../../../boost/math/special_functions/beta.hpp:832 result = 248314084.719857037067413330078125  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.043421687845020505436277602484551608  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5101148601492769792 b = 5101148601776089088  
../../../boost/math/tools/toms748_solve.hpp:262  a = 5101148601492769792 b = 5101148601776089088 d = 5101148602026467328 e = 5101148602560163840 fa = -0.015855506373285754850854800679371692 fb = 0.0068870878450205050280708007903740508 fd = 0.0447775647538200677333364296828222 fe = 0.24707209059920717808545020943711279  
../../../boost/math/tools/toms748_solve.hpp:270 q11 = -118132856.16652214527130126953125 q21 = -45509507.244052745401859283447265625 q31 = 197522360.9181285798549652099609375 d21 = -295887747.24405276775360107421875 d31 = -85796935.0818714201450347900390625  
../../../boost/math/tools/toms748_solve.hpp:278 q22 = -5096960.8247601781040430068969726562 q32 = 10535134.654597109183669090270996094 d32 = -29752293.183221567422151565551757812 q33 = 1486807.7137827444821596145629882812 c = 5101148601702313984  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148601702313984  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617170171769856  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 207871452.86896383762359619140625  
../../../boost/math/special_functions/beta.hpp:421 result = 207871452.86896383762359619140625  
../../../boost/math/special_functions/beta.hpp:831 fract = 5788412656.0320758819580078125  
../../../boost/math/special_functions/beta.hpp:832 result = 207871452.86896383762359619140625  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.035911650606378596461976115961078904  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101148601702313984 b = 5101148601776089088  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148601714552832  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617170159531008  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 214129130.393557846546173095703125  
../../../boost/math/special_functions/beta.hpp:421 result = 214129130.393557846546173095703125  
../../../boost/math/special_functions/beta.hpp:831 fract = 5776836603.0024394989013671875  
../../../boost/math/special_functions/beta.hpp:832 result = 214129130.393557846546173095703125  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.037066849057539014156148482470598537  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101148601702313984 b = 5101148601714552832  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -2.3992337717657846162177631279969334e-12  
../../../boost/math/tools/toms748_solve.hpp:262  a = 5101148601702313984 b = 5101148601714552832 d = 5101148601776089088 e = 5101148601492769792 fa = -0.00062294939362140394623068573309865315 fb = 0.00053224905753901374794168077642098069 fd = 0.0068870878450205050280708007903740508 fe = -0.015855506373285754850854800679371692  
../../../boost/math/tools/toms748_solve.hpp:270 q11 = -85796935.0818714201450347900390625 q21 = -5153964.6174816321581602096557617188 q31 = 6599890.202904880046844482421875 d21 = -66690220.617481626570224761962890625 d31 = -5638957.797095119953155517578125  
../../../boost/math/tools/toms748_solve.hpp:278 q22 = -620556.65946844650898128747940063477 q32 = 40229.655000402424775529652833938599 d32 = -444763.52461308537749573588371276855 q33 = 7189.2215408844613193650729954242706 c = 5101148601708961792  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148601708961792  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617170165122048  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 211249134.6526834666728973388671875  
../../../boost/math/special_functions/beta.hpp:421 result = 211249134.6526834666728973388671875  
../../../boost/math/special_functions/beta.hpp:831 fract = 5782124698.20494842529296875  
../../../boost/math/special_functions/beta.hpp:832 result = 211249134.6526834666728973388671875  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.036534863165138140028709301532217069  
../../../boost/math/tools/toms748_solve.hpp:399  a = 5101148601702313984 b = 5101148601708961792  
../../../boost/math/tools/toms748_solve.hpp:262  a = 5101148601702313984 b = 5101148601708961792 d = 5101148601714552832 e = 5101148601776089088 fa = -0.00062294939362140394623068573309865315 fb = 2.6316513813962050249983803951181471e-07 fd = 0.00053224905753901374794168077642098069 fe = 0.0068870878450205050280708007903740508  
../../../boost/math/tools/toms748_solve.hpp:270 q11 = -5153964.6174816321581602096557617188 q21 = -2765.8004374961997200443875044584274 q31 = 6645000.8176252935081720352172851562 d21 = -5593805.8004374960437417030334472656 d31 = -2807.1823747067319345660507678985596  
../../../boost/math/tools/toms748_solve.hpp:278 q22 = -16.80758129507823639414709759876132 q32 = 22.315518745965018609922481118701398 d32 = -19.066418464567195911740782321430743 q33 = 0.18736807825430953911016729307448259 c = 5101148601708958720  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148601708958720  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617170165125120  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 211247514.929731070995330810546875  
../../../boost/math/special_functions/beta.hpp:421 result = 211247514.929731070995330810546875  
../../../boost/math/special_functions/beta.hpp:831 fract = 5782127361.73847866058349609375  
../../../boost/math/special_functions/beta.hpp:832 result = 211247514.929731070995330810546875  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.036534566209592535257488776778700412  
../../../boost/math/tools/toms748_solve.hpp:419  a = 5101148601708958720 b = 5101148601708961792  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148601708959744  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617170165124096  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 211247930.1124025285243988037109375  
../../../boost/math/special_functions/beta.hpp:421 result = 211247930.1124025285243988037109375  
../../../boost/math/special_functions/beta.hpp:831 fract = 5782126393.18081188201904296875  
../../../boost/math/special_functions/beta.hpp:832 result = 211247930.1124025285243988037109375  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.036534644133953755873456259450904327  
../../../boost/math/tools/toms748_solve.hpp:444  a = 5101148601708958720 b = 5101148601708959744  
../../../boost/math/tools/toms748_solve.hpp:445  tol = -2.0073910406314082379851746780488988e-16  
../../../boost/math/distributions/detail/inv_discrete_quantile.hpp:284 max_iter = 90 count = 88  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148601708959744  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617170165124096  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 211247930.1124025285243988037109375  
../../../boost/math/special_functions/beta.hpp:421 result = 211247930.1124025285243988037109375  
../../../boost/math/special_functions/beta.hpp:831 fract = 5782126393.18081188201904296875  
../../../boost/math/special_functions/beta.hpp:832 result = 211247930.1124025285243988037109375  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.036534644133953755873456259450904327  
../../../boost/math/special_functions/beta.hpp:1107 a = 5101148601708958720  
../../../boost/math/special_functions/beta.hpp:1108 b = 3978617170165125120  
../../../boost/math/special_functions/beta.hpp:1109 x = 0.56181499999999995331734226056141779  
../../../boost/math/special_functions/beta.hpp:1110 inv = 1  
../../../boost/math/special_functions/beta.hpp:1111 normalised = 1  
../../../boost/math/special_functions/beta.hpp:1393 invert = 0  
../../../boost/math/special_functions/beta.hpp:314 result = 211247514.929731070995330810546875  
../../../boost/math/special_functions/beta.hpp:421 result = 211247514.929731070995330810546875  
../../../boost/math/special_functions/beta.hpp:831 fract = 5782127361.73847866058349609375  
../../../boost/math/special_functions/beta.hpp:832 result = 211247514.929731070995330810546875  
../../../boost/math/special_functions/beta.hpp:1477 fract = 0.036534566209592535257488776778700412  
5101148601708959744  
```

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-04-26 16:58:38 UTC  
> Url: https://github.com/boostorg/math/pull/1125#issuecomment-2079756280  

Thanks @mborland : looks like the ibeta is going strangely wrong, OK can you try this one for me?  Thanks again!  
  
```  
#define BOOST_MATH_INSTRUMENT  
  
#include <boost/math/distributions.hpp>  
#include <boost/core/demangle.hpp>  
#include <iostream>  
  
namespace boost::math::detail  
{  
   template <class T, class Lanczos, class Policy>  
   T ibeta_power_terms_inst(T a,  
      T b,  
      T x,  
      T y,  
      const Lanczos&,  
      bool normalised,  
      const Policy& pol,  
      T prefix = 1,  
      const char* function = "boost::math::ibeta<%1%>(%1%, %1%, %1%)")  
   {  
      BOOST_MATH_STD_USING  
  
         if (!normalised)  
         {  
            // can we do better here?  
            return pow(x, a) * pow(y, b);  
         }  
  
      T result;  // LCOV_EXCL_LINE  
  
      T c = a + b;  
  
      // combine power terms with Lanczos approximation:  
      T agh = static_cast<T>(a + Lanczos::g() - 0.5f);  
      T bgh = static_cast<T>(b + Lanczos::g() - 0.5f);  
      T cgh = static_cast<T>(c + Lanczos::g() - 0.5f);  
      BOOST_MATH_INSTRUMENT_VARIABLE(agh);  
      BOOST_MATH_INSTRUMENT_VARIABLE(bgh);  
      BOOST_MATH_INSTRUMENT_VARIABLE(cgh);  
      if ((a < tools::min_value<T>()) || (b < tools::min_value<T>()))  
         result = 0;  // denominator overflows in this case  
      else  
         result = Lanczos::lanczos_sum_expG_scaled(c) / (Lanczos::lanczos_sum_expG_scaled(a) * Lanczos::lanczos_sum_expG_scaled(b));  
      result *= prefix;  
      // combine with the leftover terms from the Lanczos approximation:  
      result *= sqrt(bgh / boost::math::constants::e<T>());  
      result *= sqrt(agh / cgh);  
      BOOST_MATH_INSTRUMENT_VARIABLE(result);  
  
      // l1 and l2 are the base of the exponents minus one:  
      T l1 = (x * b - y * agh) / agh;  
      T l2 = (y * a - x * bgh) / bgh;  
      BOOST_MATH_INSTRUMENT_VARIABLE(l1);  
      BOOST_MATH_INSTRUMENT_VARIABLE(l2);  
      if (((std::min)(fabs(l1), fabs(l2)) < 0.2))  
      {  
         // when the base of the exponent is very near 1 we get really  
         // gross errors unless extra care is taken:  
         if ((l1 * l2 > 0) || ((std::min)(a, b) < 1))  
         {  
            //  
            // This first branch handles the simple cases where either:  
            //  
            // * The two power terms both go in the same direction  
            // (towards zero or towards infinity).  In this case if either  
            // term overflows or underflows, then the product of the two must  
            // do so also.  
            // *Alternatively if one exponent is less than one, then we  
            // can't productively use it to eliminate overflow or underflow  
            // from the other term.  Problems with spurious overflow/underflow  
            // can't be ruled out in this case, but it is *very* unlikely  
            // since one of the power terms will evaluate to a number close to 1.  
            //  
            if (fabs(l1) < 0.1)  
            {  
               result *= exp(a * boost::math::log1p(l1, pol));  
               BOOST_MATH_INSTRUMENT_VARIABLE(result);  
            }  
            else  
            {  
               result *= pow((x * cgh) / agh, a);  
               BOOST_MATH_INSTRUMENT_VARIABLE(result);  
            }  
            if (fabs(l2) < 0.1)  
            {  
               result *= exp(b * boost::math::log1p(l2, pol));  
               BOOST_MATH_INSTRUMENT_VARIABLE(result);  
            }  
            else  
            {  
               result *= pow((y * cgh) / bgh, b);  
               BOOST_MATH_INSTRUMENT_VARIABLE(result);  
            }  
         }  
         else if ((std::max)(fabs(l1), fabs(l2)) < 0.5)  
         {  
            //  
            // Both exponents are near one and both the exponents are  
            // greater than one and further these two  
            // power terms tend in opposite directions (one towards zero,  
            // the other towards infinity), so we have to combine the terms  
            // to avoid any risk of overflow or underflow.  
            //  
            // We do this by moving one power term inside the other, we have:  
            //  
            //    (1 + l1)^a * (1 + l2)^b  
            //  = ((1 + l1)*(1 + l2)^(b/a))^a  
            //  = (1 + l1 + l3 + l1*l3)^a   ;  l3 = (1 + l2)^(b/a) - 1  
            //                                    = exp((b/a) * log(1 + l2)) - 1  
            //  
            // The tricky bit is deciding which term to move inside :-)  
            // By preference we move the larger term inside, so that the  
            // size of the largest exponent is reduced.  However, that can  
            // only be done as long as l3 (see above) is also small.  
            //  
            bool small_a = a < b;  
            T ratio = b / a;  
            if ((small_a && (ratio * l2 < 0.1)) || (!small_a && (l1 / ratio > 0.1)))  
            {  
               T l3 = boost::math::expm1(ratio * boost::math::log1p(l2, pol), pol);  
               BOOST_MATH_INSTRUMENT_VARIABLE(l3);  
               l3 = l1 + l3 + l3 * l1;  
               BOOST_MATH_INSTRUMENT_VARIABLE(l3);  
               l3 = a * boost::math::log1p(l3, pol);  
               BOOST_MATH_INSTRUMENT_VARIABLE(l3);  
               result *= exp(l3);  
               BOOST_MATH_INSTRUMENT_VARIABLE(result);  
            }  
            else  
            {  
               T l3 = boost::math::expm1(boost::math::log1p(l1, pol) / ratio, pol);  
               l3 = l2 + l3 + l3 * l2;  
               l3 = b * boost::math::log1p(l3, pol);  
               result *= exp(l3);  
               BOOST_MATH_INSTRUMENT_VARIABLE(result);  
            }  
         }  
         else if (fabs(l1) < fabs(l2))  
         {  
            // First base near 1 only:  
            T l = a * boost::math::log1p(l1, pol)  
               + b * log((y * cgh) / bgh);  
            if ((l <= tools::log_min_value<T>()) || (l >= tools::log_max_value<T>()))  
            {  
               l += log(result);  
               if (l >= tools::log_max_value<T>())  
                  return policies::raise_overflow_error<T>(function, nullptr, pol);  // LCOV_EXCL_LINE we can probably never get here, probably!  
               result = exp(l);  
            }  
            else  
               result *= exp(l);  
            BOOST_MATH_INSTRUMENT_VARIABLE(result);  
         }  
         else  
         {  
            // Second base near 1 only:  
            T l = b * boost::math::log1p(l2, pol)  
               + a * log((x * cgh) / agh);  
            if ((l <= tools::log_min_value<T>()) || (l >= tools::log_max_value<T>()))  
            {  
               l += log(result);  
               if (l >= tools::log_max_value<T>())  
                  return policies::raise_overflow_error<T>(function, nullptr, pol);  // LCOV_EXCL_LINE we can probably never get here, probably!  
               result = exp(l);  
            }  
            else  
               result *= exp(l);  
            BOOST_MATH_INSTRUMENT_VARIABLE(result);  
         }  
      }  
      else  
      {  
         // general case:  
         T b1 = (x * cgh) / agh;  
         T b2 = (y * cgh) / bgh;  
         l1 = a * log(b1);  
         l2 = b * log(b2);  
         BOOST_MATH_INSTRUMENT_VARIABLE(b1);  
         BOOST_MATH_INSTRUMENT_VARIABLE(b2);  
         BOOST_MATH_INSTRUMENT_VARIABLE(l1);  
         BOOST_MATH_INSTRUMENT_VARIABLE(l2);  
         if ((l1 >= tools::log_max_value<T>())  
            || (l1 <= tools::log_min_value<T>())  
            || (l2 >= tools::log_max_value<T>())  
            || (l2 <= tools::log_min_value<T>())  
            )  
         {  
            // Oops, under/overflow, sidestep if we can:  
            if (a < b)  
            {  
               T p1 = pow(b2, b / a);  
               T l3 = (b1 != 0) && (p1 != 0) ? (a * (log(b1) + log(p1))) : tools::max_value<T>(); // arbitrary large value if the logs would fail!  
               if ((l3 < tools::log_max_value<T>())  
                  && (l3 > tools::log_min_value<T>()))  
               {  
                  result *= pow(p1 * b1, a);  
               }  
               else  
               {  
                  l2 += l1 + log(result);  
                  if (l2 >= tools::log_max_value<T>())  
                     return policies::raise_overflow_error<T>(function, nullptr, pol);  // LCOV_EXCL_LINE we can probably never get here, probably!  
                  result = exp(l2);  
               }  
            }  
            else  
            {  
               // This protects against spurious overflow in a/b:  
               T p1 = (b1 < 1) && (b < 1) && (tools::max_value<T>() * b < a) ? static_cast<T>(0) : static_cast<T>(pow(b1, a / b));  
               T l3 = (p1 != 0) && (b2 != 0) ? (log(p1) + log(b2)) * b : tools::max_value<T>();  // arbitrary large value if the logs would fail!  
               if ((l3 < tools::log_max_value<T>())  
                  && (l3 > tools::log_min_value<T>()))  
               {  
                  result *= pow(p1 * b2, b);  
               }  
               else if (result != 0)  // we can elude the calculation below if we're already going to be zero  
               {  
                  l2 += l1 + log(result);  
                  if (l2 >= tools::log_max_value<T>())  
                     return policies::raise_overflow_error<T>(function, nullptr, pol);  // LCOV_EXCL_LINE we can probably never get here, probably!  
                  result = exp(l2);  
               }  
            }  
            BOOST_MATH_INSTRUMENT_VARIABLE(result);  
         }  
         else  
         {  
            // finally the normal case:  
            result *= pow(b1, a) * pow(b2, b);  
            BOOST_MATH_INSTRUMENT_VARIABLE(result);  
         }  
      }  
  
      BOOST_MATH_INSTRUMENT_VARIABLE(result);  
  
      if (0 == result)  
      {  
         if ((a > 1) && (x == 0))  
            return result;  // true zero LCOV_EXCL_LINE we can probably never get here  
         if ((b > 1) && (y == 0))  
            return result; // true zero LCOV_EXCL_LINE we can probably never get here  
         return boost::math::policies::raise_underflow_error<T>(function, nullptr, pol);  
      }  
  
      return result;  
   }  
  
}  
  
int main()  
{  
   using namespace boost::math::policies;  
   using namespace boost::math;  
     
   typedef policy<discrete_quantile<integer_round_outwards> > Policy;  
   typedef typename lanczos::lanczos<double, Policy>::type lanczos_type;  
  
   std::cout << boost::core::demangle(typeid(lanczos_type).name()) << std::endl;  
  
   std::cout << boost::math::detail::ibeta_power_terms_inst(3.9786171674284134e+18, 5.1011486044456704e+18, 0.43818500000000005, 0.56181499999999995, lanczos_type(), true, Policy()) << std::endl;  
  
   return 0;  
}  
```  
  
Output should be:  
  
```  
struct boost::math::lanczos::lanczos13m53  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:41 agh = 3978617167428413440  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:42 bgh = 5101148604445670400  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:43 cgh = 9079765771874083840  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:52 result = 149592859187.68817138671875  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:57 l1 = -6.7354244935610946714562483311327518e-10  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:58 l2 = 5.2532630585679715818840706802075706e-10  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:125 l3 = 6.7354244940602464723437159686879674e-10  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:127 l3 = -4.0374425102950054010357025410984728e-19  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:129 l3 = -1.6063438083964978186912730961921625  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:131 result = 30011286371.699993133544921875  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:238 result = 30011286371.699993133544921875  
30011286371.699993133544921875  
```

---

## Comment 5

> Username: mborland  
> Created_at: 2024-04-26 17:03:42 UTC  
> Url: https://github.com/boostorg/math/pull/1125#issuecomment-2079763436  

@jzmaddock Here it is:  
  
Clang-18  
  
```  
boost::math::lanczos::lanczos13m53  
scrap.cpp:36 agh = 3978617167428413440  
scrap.cpp:37 bgh = 5101148604445670400  
scrap.cpp:38 cgh = 9079765771874083840  
scrap.cpp:47 result = 149592859187.688201904296875  
scrap.cpp:52 l1 = -6.735424769136304369763995610412028e-10  
scrap.cpp:53 l2 = 5.2532629155828719625358083980537494e-10  
scrap.cpp:120 l3 = 6.7354243107331720116055823554634778e-10  
scrap.cpp:122 l3 = -4.6293972673147390445287398339587954e-17  
scrap.cpp:124 l3 = -184.18599442584604730654973536729813  
scrap.cpp:126 result = 1.5273896380250717763985214018598168e-69  
scrap.cpp:233 result = 1.5273896380250717763985214018598168e-69  
1.5273896380250717763985214018598168e-69  
```  
  
GCC-13  
```  
boost::math::lanczos::lanczos13m53  
scrap.cpp:36 agh = 3978617167428413440  
scrap.cpp:37 bgh = 5101148604445670400  
scrap.cpp:38 cgh = 9079765771874083840  
scrap.cpp:47 result = 149592859187.688201904296875  
scrap.cpp:52 l1 = -6.7354244935610946714562483311327518e-10  
scrap.cpp:53 l2 = 5.2532630585679715818840706802075706e-10  
scrap.cpp:120 l3 = 6.7354244940602464723437159686879674e-10  
scrap.cpp:122 l3 = -4.0374425102950054010357025410984728e-19  
scrap.cpp:124 l3 = -1.6063438083964978186912730961921625  
scrap.cpp:126 result = 30011286371.700000762939453125  
scrap.cpp:233 result = 30011286371.700000762939453125  
30011286371.700000762939453125  
```

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2024-04-26 17:47:41 UTC  
> Url: https://github.com/boostorg/math/pull/1125#issuecomment-2079824151  

Thanks Matt, this might be a tough one: the incomplete beta is basically returning garbage (all digits in the log terms cancel out).  Will have to think about this...

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2024-04-28 15:38:35 UTC  
> Url: https://github.com/boostorg/math/pull/1125#issuecomment-2081523793  

@pulver do you have any thoughts on the clang/Mac autodiff failures?  
  
I note that you're using random_device in the tests which has the drawback of rendering the test non-reproducable?  But also for example in the Hermite tests, running a fairly exhaustive search using interval arithmetic, I can find "bad" cases fairly easily, for example:  
  
```  
n = 25 x = 4.7852350966373791 err = 2.389e-07  
```  
  
The issue here, is that the Hermite is a polynomial (albeit we calculate via recurrence relations), and so the error is always absolute and relative to the largest term.  There is no way round that that I can see: if you hit an ill-conditioned polynomial there's not much we can do about it.  Is this likely to be the cause?

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2024-04-28 15:40:30 UTC  
> Url: https://github.com/boostorg/math/pull/1125#issuecomment-2081524317  

@NAThompson : cubic_roots_test is generating NaN's, but only in Clang C++20 mode: any ideas?

---

## Comment 9

> Username: NAThompson  
> Created_at: 2024-04-29 02:05:49 UTC  
> Url: https://github.com/boostorg/math/pull/1125#issuecomment-2081778285  

> @NAThompson : cubic_roots_test is generating NaN's, but only in Clang C++20 mode: any ideas?  
  
The `cubic_roots` has always been fighting the issue of small numerical errors producing complex roots, which get listed as NaNs in the API. I have no clue what that would be related to C++20, but I can say this: It's probably fine to just do `if (isnan(r)) { continue; }`-I believe we actually document this problem so it is expected.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2024-05-03 10:25:40 UTC  
> Url: https://github.com/boostorg/math/pull/1125#issuecomment-2092727412  

Apart from a network failure in drone, this is passing now... merging.

---
