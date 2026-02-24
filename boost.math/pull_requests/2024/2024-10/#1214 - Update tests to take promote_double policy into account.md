# #1214 Update tests to take promote_double policy into account [Open]

> Username: rdoeffinger  
> Created at: 2024-10-15 15:43:38 UTC  
> Updated at: 2024-11-19 03:20:18 UTC  
> Url: https://github.com/boostorg/math/pull/1214  

Makes it easier to change its default to false in the future. The remaining failing tests are test_nc_chi_squared test_bessel_j_prime and test_bessel_y_prime.  
They probably have incorrect logic for the double == long double case already now.

---

## Comment 1

> Username: rdoeffinger  
> Created_at: 2024-11-19 03:20:17 UTC  
> Url: https://github.com/boostorg/math/pull/1214#issuecomment-2484624628  

I am unable to see a connection between the test failures and this change...

---
