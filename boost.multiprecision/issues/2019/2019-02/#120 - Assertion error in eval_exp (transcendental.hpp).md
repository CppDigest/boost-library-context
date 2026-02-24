# #120 - Assertion error in eval_exp (transcendental.hpp) [Closed]

> Username: kedarbhat  
> Created at: 2019-02-28 01:44:00 UTC  
> Updated at: 2019-03-03 21:48:05 UTC  
> Closed at: 2019-03-03 21:48:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/120  

Since I can't delete this blank comment, I'll use the space.  
  
The error below is with latest Boost 1.70. I'm not sure yet if this occurs in earlier versions.

---

## Comment 1

> Username: kedarbhat  
> Created at: 2019-02-28 02:01:07 UTC  
> Updated at: 2019-02-28 16:29:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/120#issuecomment-468106063  

I think this may be similar/related to issue #23. The below code (using `boost::multiprecision::cpp_bin_float_50`) fails an invariant in `eval_exp` (in transcendental.hpp). The computation using `double` as the type is included to verify it's a valid computation. This issue was discovered when `boost::math::zeta` was specialized with `boost::multiprecision::cpp_bin_float_50`, but this issue can be reproduced simply using the `boost::multiprecision::exp` call with the problematic argument.  
  
```  
BOOST_AUTO_TEST_CASE(multiprecision_zeta) {  
  using T = boost::multiprecision::cpp_bin_float_50;  
  static constexpr auto x = 2.0;  
  static constexpr auto y = 95.0;  
  T x_mp(x);  
  T y_mp(y);  
  auto answer = std::exp(y*std::log(x));  
  BOOST_REQUIRE_CLOSE(answer, 3.9614081257131865e28, std::numeric_limits<T>::epsilon());  
  auto answer_mp = boost::multiprecision::exp(y_mp*boost::multiprecision::log(x_mp));  
  BOOST_REQUIRE_CLOSE(answer, answer_mp, std::numeric_limits<T>::epsilon());  
}  
```

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-03-03 17:08:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/120#issuecomment-469042685  

Fixed in develop.

---

## Comment 3

> Username: kedarbhat  
> Created at: 2019-03-03 21:48:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/120#issuecomment-469067867  

Thanks for the quick turnaround!
