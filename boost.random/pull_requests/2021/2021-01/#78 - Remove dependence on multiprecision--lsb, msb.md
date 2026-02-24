# #78 Remove dependence on multiprecision::lsb, msb [Merged]

> Username: pdimov  
> Created at: 2021-01-01 16:22:38 UTC  
> Updated at: 2021-02-17 12:15:53 UTC  
> Merged at: 2021-02-17 12:15:53 UTC  
> Closed at: 2021-02-17 12:15:53 UTC  
> Url: https://github.com/boostorg/random/pull/78  

This pull request removes Random's dependence on Multiprecision, breaking the dependency cycle. Cycles are a problem for package managers that ship a modular Boost.  
  
By explicitly throwing `std::range_error` from the helper lsb/msb functions it also fixes test errors in sobol_validate and niederreiter_base2_validate introduced by Multiprecision's functions being recently changed to (correctly) throw `std::domain_error` (input out of range) instead of `std::range_error` (output out of range).  
  
I note that these generators also throw `std::range_error` on invalid seeds, which should in theory be `std::domain_error`, but fixing that is outside the scope of this PR.

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-01-01 16:43:34 UTC  
> Url: https://github.com/boostorg/random/pull/78#issuecomment-753342212  

(For the Multiprecision `std::range_error` change, see https://github.com/boostorg/multiprecision/issues/257 and https://github.com/boostorg/multiprecision/pull/281.)

---
