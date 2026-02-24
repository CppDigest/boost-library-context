# #53 Fix breakage in replaced adaptor for C++03 [Merged]

> Username: morinmorin  
> Created at: 2017-07-12 13:17:06 UTC  
> Updated at: 2017-07-12 13:21:41 UTC  
> Merged at: 2017-07-12 13:18:06 UTC  
> Closed at: 2017-07-12 13:18:06 UTC  
> Url: https://github.com/boostorg/range/pull/53  

This fixes a C++03 breakage introduced in #48.  
  
Additionally, this PR adds `Value` template parameter to `operator|(rng, replaced_if)` to make it consistent with `operator|(rng, replaced)`.

---

## Comment 1

> Username: morinmorin  
> Created_at: 2017-07-12 13:21:41 UTC  
> Url: https://github.com/boostorg/range/pull/53#issuecomment-314767094  

Thanks for the quick merge!

---
