# #1040 - More constexpr for <cmath> and <complex> [Open]

> Username: mborland  
> Created at: 2023-10-30 07:41:27 UTC  
> Updated at: 2023-10-31 07:08:36 UTC  
> Url: https://github.com/boostorg/math/issues/1040  

It looks like the following paper has been accepted: https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2023/p1383r2.pdf. Since others depend on our special functions in their standard libraries we should probably implement this.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-10-30 18:39:26 UTC  
> Url: https://github.com/boostorg/math/issues/1040#issuecomment-1785828703  

I don't *think* anyone is using our versions of the functions listed in the paper, but we should probably experiment with this anyway: both for our versions of things listed here (expm1, log1p, erf, tgamma etc) and for other relatively trivial utility functions.  Possibly anything that has a simple rational approximation actually.  
  
I have two questions for which I confess I've forgotten the answers:  
  
1) If a function is declared `constexpr` and calls say a non-constexpr version of `pow`, can a compiler decline to compile it even if not used in a constexpr context?  
2) Even if we're OK on (1), can a compilers optimizer say "Oh goody, a constexpr function, lets optimize that away" even though the context isn't formally constexpr?  
  
If either are true then we will need some annoying configuration logic and macro-ization.  
  
I'm hoping not though... thinking about it, constexpr template functions which are only constexpr depending on certain template arguments would otherwise be impossible?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2023-10-30 19:38:29 UTC  
> Updated at: 2023-10-31 07:07:20 UTC  
> Url: https://github.com/boostorg/math/issues/1040#issuecomment-1785915578  

I've speculated a bit on this in the past times.  
  
I think in C++26 basically most (or all) of the `<cmath>` functions are supposed to go `constexpr`. We need someone more dialed into LEWG to really confirm this.  
  
So, since `tgamma` is present in Math's specfun, it should theoretically go `constexpr` for C++26. But then when that is the case all of `<cmath>` should, at the time of that also be `constexpr´.  
  
I do not think this applies to those more advanced functions that used to be in TR1.  
  
But I guess for POD, it might make sense to get a `constexpr` version of `tgamma` and any other specfun functions that overlap with those found direclty in `<cmath>`.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2023-10-31 07:08:36 UTC  
> Url: https://github.com/boostorg/math/issues/1040#issuecomment-1786581805  

I guess this might be something that finally decides over keeping/not-keeping `acosh`, `asinh` and `atanh` in Math. These, I believe, might also need to go `constexpr`.
