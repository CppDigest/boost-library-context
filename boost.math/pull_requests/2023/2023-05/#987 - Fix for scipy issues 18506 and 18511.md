# #987 Fix for scipy issues 18506 and 18511 [Merged]

> Username: mborland  
> Created at: 2023-05-23 10:40:57 UTC  
> Updated at: 2023-05-31 07:39:44 UTC  
> Merged at: 2023-05-31 07:39:40 UTC  
> Closed at: 2023-05-31 07:39:40 UTC  
> Url: https://github.com/boostorg/math/pull/987  

All of the member variables of the hyper geometric distribution were of type `unsigned` leading to overflow when `n*N` exceeded `UINT_MAX`. Replace them with `std::uint64_t`. Similar to https://github.com/boostorg/math/pull/939  
  
x-ref: https://github.com/scipy/scipy/issues/18506 and https://github.com/scipy/scipy/issues/18511

---

## Comment 1

> Username: mborland  
> Created_at: 2023-05-23 13:19:31 UTC  
> Url: https://github.com/boostorg/math/pull/987#issuecomment-1559333422  

@jzmaddock Can you please take a look at this?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2023-05-23 16:46:02 UTC  
> Url: https://github.com/boostorg/math/pull/987#issuecomment-1559810068  

OK this is nearly right (but see below).  
  
The changes to use [u]int64_t are fine.  
  
However, matching changes are required in detail/hypergeometric_pdf.hpp, detail/hypergeometric_cdf.hpp and detail/hypergeometric_quantile.hpp otherwise the arguments will simply get narrowed to `unsigned` internally.  
  
The changes to some of the functions are not, consider the original version of variance:  
  
```  
   template <class RealType, class Policy>  
   inline RealType variance(const hypergeometric_distribution<RealType, Policy>& dist)  
   {  
      RealType r = static_cast<RealType>(dist.defective());  
      RealType n = static_cast<RealType>(dist.sample_count());  
      RealType N = static_cast<RealType>(dist.total());  
      return n * r  * (N - r) * (N - n) / (N * N * (N - 1));  
   } // RealType variance(const hypergeometric_distribution<RealType, Policy>& dist)  
```  
  
This I think does the right thing - which is to say carry out all computations as floating point arithmetic, and your changes I think break that.  
  
The reason the old `mean` was failing in https://github.com/scipy/scipy/issues/18511 was because we hadn't applied this workaround there, and computation was as integers:  
  
```  
   template <class RealType, class Policy>  
   inline RealType mean(const hypergeometric_distribution<RealType, Policy>& dist)  
   {  
      return static_cast<RealType>(dist.defective() * dist.sample_count()) / dist.total();  
   } // RealType mean(const hypergeometric_distribution<RealType, Policy>& dist)  
```  
  
So that should use a bit of casting as per the original variance implementation to carry out FP arithmetic not integer.  
  
Now for the open question:  I assume that we're calculating the complement of the CDF here?  And that the OP in https://github.com/scipy/scipy/issues/18506 is just upping the population size until some limit is reached (or something breaks)?   If so then using int64_t will help, but we will still break in the end... but I'm conflicted here because the arguments are logically integers!

---

## Comment 3

> Username: mborland  
> Created_at: 2023-05-24 07:49:38 UTC  
> Updated_at: 2023-05-24 07:50:03 UTC  
> Url: https://github.com/boostorg/math/pull/987#issuecomment-1560620065  

> OK this is nearly right (but see below).  
  
Hit all of the changes in the latest commit.  
  
>   
> Now for the open question: I assume that we're calculating the complement of the CDF here? And that the OP in [scipy/scipy#18506](https://github.com/scipy/scipy/issues/18506) is just upping the population size until some limit is reached (or something breaks)? If so then using int64_t will help, but we will still break in the end... but I'm conflicted here because the arguments are logically integers!  
  
I think like https://github.com/boostorg/math/pull/939 we try bumping it up to `uint64_t`, and see if the users hit another wall. We could try conditionally using `__int128`, but I am not quite sure how pybind11 works with extension types.

---

## Comment 4

> Username: WarrenWeckesser  
> Created_at: 2023-05-24 18:12:22 UTC  
> Url: https://github.com/boostorg/math/pull/987#issuecomment-1561721775  

+1 for changing the type to `uint64_t` (and for fixing the mean calculation).  This moves the "breaking point" pretty far out there, and it seems like a reasonable change even without the motivation from the SciPy issues.  
  
On the SciPy side, we need to ensure that Python integers that are too large to be represented as 64 bit integers are handled appropriately instead of allowing them to end up in 64 bit variables.

---

## Comment 5

> Username: mborland  
> Created_at: 2023-05-25 07:16:21 UTC  
> Url: https://github.com/boostorg/math/pull/987#issuecomment-1562400333  

@jzmaddock The only CI failure is fail to clone so this should be good if you want to take another look.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2023-05-25 17:45:16 UTC  
> Url: https://github.com/boostorg/math/pull/987#issuecomment-1563281415  

Looks good to me - we just need to update the docs to `int64_t`.

---
