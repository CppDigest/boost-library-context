# #338 Target attribute definition [Open]

> Username: nemothenoone  
> Created at: 2020-07-24 20:08:51 UTC  
> Updated at: 2020-08-17 22:21:00 UTC  
> Url: https://github.com/boostorg/config/pull/338  

Target attribute definition introduces function multi-versioning.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-07-26 19:05:30 UTC  
> Url: https://github.com/boostorg/config/pull/338#issuecomment-664027781  

What's the motivation for adding this to Boost.Config rather than the specific library that needs it - or is there a need across multiple libraries?

---

## Comment 2

> Username: nemothenoone  
> Created_at: 2020-07-26 19:52:15 UTC  
> Updated_at: 2020-07-26 22:09:10 UTC  
> Url: https://github.com/boostorg/config/pull/338#issuecomment-664032532  

Hm. Good question. I was thinking of some kind of architecture-specific function selection macro for Boost.Multiprecision, Boost.Math and for one more library coming. I think lots of architecture-specific places (e.g. with intrinsics) across the whole Boost suite could use this macro.  
  
I believe the statement above requires some particular proofs of this being needed, so I don't mind if this does not gets accepted until I provide those.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2020-07-27 17:32:19 UTC  
> Url: https://github.com/boostorg/config/pull/338#issuecomment-664534618  

My inclination is to not merge this at this time - the rational is:  
  
* It's completely compiler specific (not necessarily a show stopper).  
* It's not required by multiple Boost libraries yet.  
  
But... I can see the logic so let's leave it open for now.  
  
What tends to happen with rather specific stuff like this, is they get trialled in a particular library first, and then moved here when more than one library needs them, or else when the configuration is clearly more easily done via the logic that Boost.Config already has.  
  
With regard to multiprecision, what we've done so far is just tip a toe into the water of using intrinsics, but these are abstracted so that the actual code doesn't have to deal with 32/64 bit issues, or compiler specific stuff (ie works with msvc as well as gcc): https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/cpp_int/intel_intrinsics.hpp.

---
