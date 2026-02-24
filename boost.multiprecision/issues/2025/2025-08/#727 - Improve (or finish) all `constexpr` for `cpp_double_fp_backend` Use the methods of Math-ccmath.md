# #727 - Improve (or finish) all `constexpr` for `cpp_double_fp_backend` Use the methods of Math-ccmath. [Closed]

> Username: ckormanyos  
> Created at: 2025-08-17 07:13:10 UTC  
> Updated at: 2025-09-01 17:27:40 UTC  
> Closed at: 2025-09-01 17:27:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/727  

This issue tracks improving (or finishing) `constexpr` for all of `cpp_double_fp_backend`.  
  
Use the methods of Math-ccmath, in particular if something is constant-evaluated, allowing for `constexpr`built-in `<cmath>`-like functions (needed in the details of the double-fp backend.  
  
See also #703
