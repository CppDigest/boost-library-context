# #406 - Crash / assertion failure calculating T-distribution quantile [Closed]

> Username: evanmiller  
> Created at: 2020-07-23 22:27:44 UTC  
> Updated at: 2020-07-27 18:35:42 UTC  
> Closed at: 2020-07-27 18:35:41 UTC  
> Url: https://github.com/boostorg/math/issues/406  

This is a customer-reported bug and so I don't have a full test case. However, I have the following backtrace:  
  
```  
Application Specific Information:  
Assertion failed: (*p_derivative >= 0), function ibeta_a_step, file /usr/include/boost/math/special_functions/beta.hpp, line 739.  
   
  
Thread 0 Crashed:: Dispatch queue: com.apple.main-thread  
0   libsystem_kernel.dylib        	0x00007fff6b70d33a __pthread_kill + 10  
1   libsystem_pthread.dylib       	0x00007fff6b7c9e60 pthread_kill + 430  
2   libsystem_c.dylib             	0x00007fff6b694808 abort + 120  
3   libsystem_c.dylib             	0x00007fff6b693ac6 __assert_rtn + 314  
4   libcrit.dylib                 	0x000000010496bef4 double boost::math::detail::ibeta_a_step<double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(double, double, double, double, int, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&, bool, double*) + 196  
5   libcrit.dylib                 	0x000000010495aa36 double boost::math::detail::ibeta_imp<double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(double, double, double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&, bool, bool, double*) + 4598  
6   libcrit.dylib                 	0x0000000104958e53 double boost::math::detail::fast_students_t_quantile_imp<double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(double, double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&, boost::integral_constant<bool, true> const*) + 611  
7   libcrit.dylib                 	0x0000000104958bdd double boost::math::detail::fast_students_t_quantile<double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(double, double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) + 45  
8   libcrit.dylib                 	0x0000000104958a94 double boost::math::quantile<double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(boost::math::students_t_distribution<double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> > const&, double const&) + 356  
9   libcrit.dylib                 	0x000000010493f3d0 double boost::math::quantile<double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(boost::math::complemented2_type<boost::math::students_t_distribution<double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >, double> const&) + 32  
```  
  
I'm using my [jacobi-theta](https://github.com/evanmiller/math/tree/jacobi-theta) branch and the assertion failure occurs on this line:  
  
https://github.com/evanmiller/math/blob/98bc16c70a1ce9730a292931138b6bf827dd908c/include/boost/math/special_functions/beta.hpp#L739  
  
This code is orthogonal to the Jacobi Theta work and so I don't suspect it. However, it may have something to do with the recent double (non-)promotion work, some of which I have merged in.  
  
I'm reporting the crash now in case anyone has a fast idea. I will report more information as I get it from the customer, and will provide a test case if I can devise one.

---

## Comment 1

> Username: evanmiller  
> Created at: 2020-07-23 22:49:19 UTC  
> Url: https://github.com/boostorg/math/issues/406#issuecomment-663269221  

Also, while I'm rooting around in beta.hpp, it would be great to get #372 fixed. (I think it was waiting on test coverage.)

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-07-24 07:34:34 UTC  
> Url: https://github.com/boostorg/math/issues/406#issuecomment-663387683  

Hmmm, I'm wondering if that assertion can't just be removed.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-07-24 10:05:09 UTC  
> Url: https://github.com/boostorg/math/issues/406#issuecomment-663465574  

>Hmmm, I'm wondering if that assertion can't just be removed.  
  
No my bad, the condition is >= 0 : which definitely should be true.  My guess is the power terms came out as a NaN and something has gone terribly wrong, but I would need a test case to diagnose I think.  
  
BTW I don't think this has anything to do with the long double promotion, as the tests are all fine on platforms where double and long double are the same type.  Test coverage is pretty thorough, though it's hard to be definitive when there are 3 degrees of freedom in the arguments as the potential test space is so huge :(  I would suspect an extreme / corner case argument somewhere, but I couldn't guess in which argument!

---

## Comment 4

> Username: evanmiller  
> Created at: 2020-07-24 11:07:51 UTC  
> Url: https://github.com/boostorg/math/issues/406#issuecomment-663487261  

Thanks. Because the highest level call is a quantile, there should be only two 2 arguments in the test space (DoF and quantile).  
  
Based on the surrounding code, I am fairly confident that:  
  
* DoF > 0  
* quantile is 0.0025  
  
 I'll leave this open for now and let you know if I make any progress.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-07-24 16:28:20 UTC  
> Url: https://github.com/boostorg/math/issues/406#issuecomment-663622754  

I have a test case where the degree of freedom is a denorm and p = 0.0025.

---

## Comment 6

> Username: evanmiller  
> Created at: 2020-07-24 16:58:21 UTC  
> Url: https://github.com/boostorg/math/issues/406#issuecomment-663635702  

FWIW I am passing in integers, so I don't *think* a denorm DoF is the trigger in the case I saw. Here is the relevant code:  
  
```C++  
typedef boost::math::policies::policy<  
      boost::math::policies::promote_double<false>  
      > numeric_policy;  
  
double critical_value_t(double p_value, long nu) {  
    if (nu <= 0 || isnan(p_value))  
        return NAN;  
    boost::math::students_t_distribution<double, numeric_policy> dist(nu);  
    return boost::math::quantile(boost::math::complement(dist, 0.5*p_value));  
}  
```  
  
In the relevant backtrace `p_value` is always 0.005 hence quantile is expected to be 0.0025. It's worth noting that I'm using the `complement` in the code above, so maybe 0.9975 will be a more relevant test value. I've been testing integer DoFs but so far have been unable to come up with a test case.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2020-07-27 17:36:08 UTC  
> Url: https://github.com/boostorg/math/issues/406#issuecomment-664536483  

Have you had any luck finding a test case with integer DoF?  I tried a comprehensive search through all the 64-bit integer values but had to give up as it was going to take pretty much forever :(

---

## Comment 8

> Username: evanmiller  
> Created at: 2020-07-27 18:27:19 UTC  
> Url: https://github.com/boostorg/math/issues/406#issuecomment-664563502  

> Have you had any luck finding a test case with integer DoF? I tried a comprehensive search through all the 64-bit integer values but had to give up as it was going to take pretty much forever :(  
  
I also gave up on finding an integer test case. I'm really stumped! But I will plan to incorporate your latest fixes and see if I get any similar crash reports. (This is not super-urgent as the client code is still in beta.)

---

## Comment 9

> Username: jzmaddock  
> Created at: 2020-07-27 18:35:41 UTC  
> Url: https://github.com/boostorg/math/issues/406#issuecomment-664567693  

OK, current fix merged, closing this for now, please re-open if there's a test case or the problem returns.
