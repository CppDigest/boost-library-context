# #744 - Eigen NumTraits specialisation doesn't define max_digits10() [Open]

> Username: FreddieWitherden  
> Created at: 2025-10-14 22:33:29 UTC  
> Updated at: 2025-10-14 22:33:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/744  

The NumTraits specialisation which Boost multiprecision provides as part of its Eigen integration is missing a few member functions.  One of these is max_digits10 which is required by Eigen 5 for printing matrices containing multiprecision scalars.  
  
Corresponding Eigen issue:  
  
https://gitlab.com/libeigen/eigen/-/issues/2985  
  
where it seems the solution is to either inherit from the base class (to bring in a default) or explicit implement the relevant methods.
