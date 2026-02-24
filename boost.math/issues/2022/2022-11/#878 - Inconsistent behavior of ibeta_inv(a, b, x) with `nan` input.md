# #878 - Inconsistent behavior of ibeta_inv(a, b, x) with `nan` input [Closed]

> Username: WarrenWeckesser  
> Created at: 2022-11-14 17:12:22 UTC  
> Updated at: 2022-11-26 18:24:52 UTC  
> Closed at: 2022-11-26 18:24:52 UTC  
> Url: https://github.com/boostorg/math/issues/878  

I don't know if this qualifies as a bug, or is undefined behavior as far as boost/math is concerned, but I get three different behaviors from `ibeta_inv(nan, 2, 0.5)`, `ibeta_inv(2, nan, 0.5)` and `ibeta_inv(2, 2, nan)`:  
  
* `ibeta_inv(nan, 2, 0.5)` does not raise an exception; `nan` is returned.  
* `ibeta_inv(2, nan, 0.5)` raises:  
  
  ```  
  ibeta_inv: /home/warren/repos/git/forks/boost/math/include/boost/math/special_functions/beta.hpp:723:  
  T boost::math::detail::ibeta_fraction2(T, T, T, T, const Policy&, bool, T*)  
  [with T = long double; Policy = boost::math::policies::policy<boost::math::policies::promote_float<false>, boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>]:  
  Assertion `*p_derivative >= 0' failed.  
  ```  
* `ibeta_inv(2, 2, nan)` raises:  
  
  ```  
  terminate called after throwing an instance of 'boost::wrapexcept<boost::math::rounding_error>'  
  what():  Error in function boost::math::trunc<long double>(long double):  
  Value nan can not be represented in the target integer type.  
  ```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-11-14 18:03:44 UTC  
> Url: https://github.com/boostorg/math/issues/878#issuecomment-1314165860  

I would say it's a bug, and should probably raise a domain error in all 3 cases.  Will investigate.
