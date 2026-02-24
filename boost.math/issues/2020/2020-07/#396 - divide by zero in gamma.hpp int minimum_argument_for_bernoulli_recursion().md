# #396 - divide by zero in gamma.hpp int minimum_argument_for_bernoulli_recursion() [Closed]

> Username: deh00  
> Created at: 2020-07-08 02:04:33 UTC  
> Updated at: 2020-12-24 18:35:48 UTC  
> Closed at: 2020-12-24 18:35:48 UTC  
> Url: https://github.com/boostorg/math/issues/396  

EDIT: the minimum code reproduce I posted did not take into account other linkages I had. Removing it from this post. Sorry for the confusion. The issue is still valid however.  
  
I am experiencing a crash at program start up time. Some static initialization I can't chase down is resulting in the following stack::  
  
detail::minimum_argument_for_bernoulli_recursion (line 374).  
detail::regularised_gamma_prefix<>  
detail::gamma_incomplete_imp<>  
math::gamma_p<>  
init::do_init<>  
init::init  
igamma_initializer<>::initializer  
  
the offending type is "class boost::multiprecision::number<class boost::multiprecision::backends::cpp_dec_float<100,int,void>,1>"  
  
relevant values here are:  
boost::math::tools::digits<T>() = 335  
std::ldexp(1.0f, 1-boost::math::tools::digits<T>()) = 0  
  
this function was changed from v. 1.71 to 1.72.  
  
Using the following:  
Microsoft Visual Studio Professional 2019  
Version 16.4.5  
vcpkg 2ad4074aea2415e962153544ca942942e7d52323  
boost 1.73.0

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-07-08 02:22:10 UTC  
> Url: https://github.com/boostorg/math/issues/396#issuecomment-655241322  

What value causes this bug to be hit?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-07-08 07:21:45 UTC  
> Url: https://github.com/boostorg/math/issues/396#issuecomment-655337987  

@NAThompson  I'm not sure it's a specific value - it's program initialization code.  There is a  related issue open where a type with loads of digits precision but very restricted exponent range break this code in a way I have no fix for.  This doesn't look like that though, and this should be tested already really...

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-07-08 12:00:21 UTC  
> Url: https://github.com/boostorg/math/issues/396#issuecomment-655474489  

Ah, I mis-read your post, it *might* be an order of initialization thing, but I don't see how/why looking at the code.  
  
Are you able to produce a test case we can reproduce with because at present it "works for me".

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-07-27 17:37:18 UTC  
> Url: https://github.com/boostorg/math/issues/396#issuecomment-664537062  

@deh00 : ping?

---

## Comment 5

> Username: deh00  
> Created at: 2020-07-27 18:33:10 UTC  
> Url: https://github.com/boostorg/math/issues/396#issuecomment-664566515  

Sorry I've been busy. I still owe you minimum code to reproduce, My project has too many libraries in play. In the meantime I am working around my crash as follows:  
  
template<class T>  
int minimum_argument_for_bernoulli_recursion()  
{  
   const float digits10_of_type = (std::numeric_limits<T>::is_specialized  
                                      ? static_cast<float>(std::numeric_limits<T>::digits10)  
                                      : static_cast<float>(boost::math::tools::digits<T>() * 0.301F));  
  
   const auto denom = std::ldexp(1.0f, 1-boost::math::tools::digits<T>());  
  
   if (denom == 0)  
   {  
     return (int) (digits10_of_type*1.7);  
   }  
  
   const float limit = std::ceil(std::pow(1.0f / denom, 1.0f / 20.0f));  
  
   return (int)((std::min)(digits10_of_type * 1.7F, limit));  
}

---

## Comment 6

> Username: ckormanyos  
> Created at: 2020-08-11 18:19:08 UTC  
> Url: https://github.com/boostorg/math/issues/396#issuecomment-672156940  

> I still owe you minimum code to reproduce...  
  
I just reviewed this post with renewed interest.  
  
- Are you using multiprecision types or solely built-ins?  
- Are you able to generate a small(er) program for reproducing this phenomenon?

---

## Comment 7

> Username: deh00  
> Created at: 2020-09-04 07:25:09 UTC  
> Url: https://github.com/boostorg/math/issues/396#issuecomment-686967283  

#include "float.h"  
  
#include "boost/multiprecision/cpp_dec_float.hpp"  
#include "boost/math/distributions/beta.hpp"  
  
  
struct turn_on_floating_exceptions {  
  turn_on_floating_exceptions() {  
    int cw = _controlfp( 0, 0 );  
    cw &=~(EM_INVALID | EM_OVERFLOW | EM_ZERODIVIDE );  
    _controlfp( cw, MCW_EM );  
  }  
};  
turn_on_floating_exceptions yes_turn_on_floating_exceptions;  
  
int main(void)  
{  
  using namespace boost::math;  
  using namespace boost::multiprecision;  
  
  using b_dist_type = beta_distribution<cpp_dec_float_100>;  
  b_dist_type b_dist(1.0,2.0);  
  auto test = -boost::math::cdf(boost::math::complement(b_dist,0.5));  
  
  return 0;  
}

---

## Comment 8

> Username: ckormanyos  
> Created at: 2020-09-04 18:36:02 UTC  
> Url: https://github.com/boostorg/math/issues/396#issuecomment-687314782  

> Are you able to generate a small(er) program for reproducing this phenomenon?  
  
Thank you for the sample provided above.  
  
With your reduced sample code, on VS14.2 (VC2019), I can successfully obtain _program exit with exception_ prior to the jump to `main()`.  
  
I will investigate this. If it is divide by zero, as you seem to suggest, I will start by looking for that first.

---

## Comment 9

> Username: ckormanyos  
> Created at: 2020-09-06 13:57:04 UTC  
> Updated at: 2020-09-06 13:57:40 UTC  
> Url: https://github.com/boostorg/math/issues/396#issuecomment-687796482  

This issue seemed surprising to me. It was, however, clear and straightforward to reproduce with the user's supplied reduced sample code.  
  
A first fix is provided in #433   
  
@deh00 If possible, could you please try the fix in #433?

---

## Comment 10

> Username: deh00  
> Created at: 2020-09-14 18:14:22 UTC  
> Url: https://github.com/boostorg/math/issues/396#issuecomment-692225895  

> @deh00 If possible, could you please try the fix in #433?  
  
@ckormanyos thanks for fix. Unfortunately I won't have a chance to try anytime soon... I would say if that sample code works now then it should work for me.
