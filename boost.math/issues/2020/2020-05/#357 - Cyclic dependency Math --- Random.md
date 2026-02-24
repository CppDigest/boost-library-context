# #357 - Cyclic dependency Math <-> Random [Open]

> Username: Kojoley  
> Created at: 2020-05-26 16:19:04 UTC  
> Updated at: 2020-05-27 12:34:04 UTC  
> Url: https://github.com/boostorg/math/issues/357  

The only use of Random is `boost/random/uniform_real_distribution.hpp` in `boost/math/tools/ulps_plot.hpp`. I am not familiar with the code, but may be there is a way to rewrite it without using Random?

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-05-26 16:56:29 UTC  
> Url: https://github.com/boostorg/math/issues/357#issuecomment-634148831  

We could use `std::random` with a bunch of casting, though we wouldn't be able to query abscissas that are not representable as `float`, `double`, and `long double`.  
  
Is it super important that we don't cross-use features from other boost libraries? One of the main reasons I enjoy developing in boost is that I can benefit from a wide variety of things within the library.

---

## Comment 2

> Username: Kojoley  
> Created at: 2020-05-26 17:49:41 UTC  
> Url: https://github.com/boostorg/math/issues/357#issuecomment-634177091  

It depends, you might saw reoccurring mailing list posts about that, as well as currently ongoing discussion. For me, it will not after #151 is resolved, but as for now the new header introduced two new dependencies, and one of which is cyclic, what usually indicates a wrong logical split between libraries.

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-05-26 18:02:26 UTC  
> Url: https://github.com/boostorg/math/issues/357#issuecomment-634184586  

Ok, if this is gonna make a mess, I'll go ahead and try to clean it up. This hasn't been released yet, so we can make backwards-incompatible changes.  
  
Let me think about how this should be done; it's not obvious to me.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-05-26 18:06:49 UTC  
> Url: https://github.com/boostorg/math/issues/357#issuecomment-634187431  

@NAThompson : one of the ideas I've been toying with (which I think you first suggested!) was having the option for Math and Multiprecision to sit standalone without the rest of Boost if required.  It would require the transition to C++11 or later next year though...

---

## Comment 5

> Username: NAThompson  
> Created at: 2020-05-27 02:32:12 UTC  
> Url: https://github.com/boostorg/math/issues/357#issuecomment-634390469  

@jzmaddock : Is there any way we could do a module while preserving what we have for C++11 users?

---

## Comment 6

> Username: jzmaddock  
> Created at: 2020-05-27 12:34:04 UTC  
> Url: https://github.com/boostorg/math/issues/357#issuecomment-634629438  

> Is there any way we could do a module while preserving what we have for C++11 users?  
  
I confess not to know enough about the current modules proposal to say for sure - but I sure hope so!
