# #269 Feature : support for fmod free function from within the math namespace [Merged]

> Username: mkaravel  
> Created at: 2015-03-13 10:46:53 UTC  
> Updated at: 2015-04-05 10:32:03 UTC  
> Merged at: 2015-04-04 23:46:19 UTC  
> Closed at: 2015-04-04 23:46:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/269  

Add support for calls to `boost::geometry::math::fmod`.  
- For floating-point numbers it calls `std::fmod`.  
- For fundamental integral numbers it calls `operator%`.  
- For user defined number types the free function `fmod` is called (lookup is activated in the global namespace, the `std` namespace and the type's namespace.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-03-14 18:24:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/269#issuecomment-80656211  

Since the intention is to add a function working for arbitrary numerical types maybe it should have more generic name, e.g. `mod()`?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-03-15 04:31:19 UTC  
> Updated_at: 2015-03-16 10:10:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/269#discussion_r26444467  

`sqrt` should be renamed to `fmod`, `function` or something similar.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-03-15 04:44:49 UTC  
> Updated_at: 2015-03-16 10:10:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/269#discussion_r26444529  

So here IsIntegral could be initialized to boost::is_integral<Fundamental>::value by default.  
  
But actually, I think in general we're specializing math functions for floating-point types. I'm aware that this is done this way in order to support `ttmath::Big` above in the `modulo` struct. So for user-defined types it's required that there should be a function `fmod()`. I think that we should consider the implementation of 3 versions:  
- for built-in integrals - using `operator%`  
- for built-in floating-point types - calling `fmod()`  
- for user-defined types - calling `mod()` because it's more generic name (and maybe additionally a default-version of this function which could be found by the ADL, which'd use operator% or alternativaly call a member function of user-defined type)

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-03-16 08:33:56 UTC  
> Updated_at: 2015-03-16 10:10:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/269#discussion_r26467235  

Thanks for catching this. Typo introduced by careless copy/paste....  
Will fix it.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-03-16 08:51:28 UTC  
> Updated_at: 2015-03-16 10:10:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/269#discussion_r26468032  

True: `IsIntergral` can be initialized as you propose. Will do it your way.  
  
All three versions you mention are already implemented, the difference being that for user defined functions I assume the presence of `fmod()` rather than `mod()`.  
I agree that `mod()` is a more generic name. The only instance of user-defined number type that I have and have used is `ttmath::Big<>`, and for those I have added myself the missing function (see PR #268). So we are pretty flexible here. Using `mod()` as the required function is okay with me and will do the change.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-03-16 08:53:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/269#issuecomment-81510824  

I used `fmod()` to basically stress that this would work with floating-point numbers as well; it is true that `fmod()` is misleading for intergal types.  
Using `mod()` as the generic name is okay with me, and will make this change.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-03-16 09:54:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/269#issuecomment-81550177  

@awulkiew All your comments have been addressed in commit d12f80f.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-03-16 10:17:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/269#issuecomment-81558994  

@awulkiew I had to overwrite the last commit. So the commit that implements the changes is bf96857.

---
