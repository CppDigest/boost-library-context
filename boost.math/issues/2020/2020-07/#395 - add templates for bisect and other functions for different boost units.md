# #395 - add templates for bisect and other functions for different boost units [Open]

> Username: L0ric0  
> Created at: 2020-07-07 09:34:59 UTC  
> Updated at: 2021-01-23 06:39:30 UTC  
> Url: https://github.com/boostorg/math/issues/395  

the `boost::math::tools::bisect` function returns `std::pair<T, T>` at the moment. this is ok when using c integral types for the functions. when trying to use boost units for this it is impossible to for a function that has the following name: `boost::units::quantity<boost::units::si::length, double> f(boost::units::quantity<boost::units::si::time, double> t)` as this is a common occurance when using boost units it wouldbe nice to have an additional template for this case (probably not only for the bisect method but all methods where the units could be different).  
  
an example repository that shows this problem for `bisect` and `barycentric_rational` can be found at https://github.com/L0ric0/boostorg-mat-issue-314  
  
related to #314

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-07-07 10:51:28 UTC  
> Url: https://github.com/boostorg/math/issues/395#issuecomment-654770353  

This would be tricky to program correctly in general, but I think you're correct that this would be useful, and the correct method would be to use decltype post C++11 to get the function return type and meta-program from there to fix up the correct types.

---

## Comment 2

> Username: L0ric0  
> Created at: 2020-07-07 11:56:42 UTC  
> Url: https://github.com/boostorg/math/issues/395#issuecomment-654805219  

to not use decltype and to make the implementation easier for now one could let the user declare the return type as a template argument (as for all use cases where units are used it should be known)

---

## Comment 3

> Username: mborland  
> Created at: 2020-07-10 04:13:09 UTC  
> Url: https://github.com/boostorg/math/issues/395#issuecomment-656472274  

@jzmaddock The return of `boost::math::tools::bisect` is `std::pair<T, T>` but it in all test cases, and the example from @L0ric0 `.first` is immediately called from the return. Is `.second` ever a useful return value? If it is not changing bisect to `auto -> decltype(f)`, removing all the instances of `std::make_pair()` and just returning what would have been `.first` resolves the issue. It would also be compliant with C++11. As for `barycentric_rational` I will have to look more thoroughly because it is part of a class and not standalone.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-07-10 08:22:42 UTC  
> Url: https://github.com/boostorg/math/issues/395#issuecomment-656552231  

Bisect naturally returns an interval which contains the true root - IMO yes .second can be useful, especially if the procedure terminates prior to finding a tight interval.  I can see how to program the return type (probably via a trait), I just need to find some time ;)

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-07-29 16:50:23 UTC  
> Url: https://github.com/boostorg/math/issues/395#issuecomment-665778874  

Having now looked into this some more, I see that it's a non-starter, I just can't get dimensioned quantities to work inside bisect>  Among other things:  
  
* No comparison to literal zero, and no alternative (ie not usable in boolean contexts).  
* No ability to check that the endpoints bracket the root (related to no comparisons).  
* No constructor from a scalar either, so we can't construct a dimensioned zero.  
* No concept of epsilon.  
  
There are other complications too, some I can work around others not so much.  The inability to check on the sign of a dimensioned value is a killer though.

---

## Comment 6

> Username: Rashika101  
> Created at: 2021-01-23 06:39:30 UTC  
> Url: https://github.com/boostorg/math/issues/395#issuecomment-765878103  

Hey!!  
Are you willing to accept new contributors?  
 I am interested into contributing to this repository so, if you could explain a bit more this feature, it would be great.  
Thanks
