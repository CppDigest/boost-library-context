# #1044 Fix detection of ccmath usage in trunc [Merged]

> Username: mborland  
> Created at: 2023-11-03 07:35:23 UTC  
> Updated at: 2023-11-03 20:03:14 UTC  
> Merged at: 2023-11-03 10:32:21 UTC  
> Closed at: 2023-11-03 10:32:21 UTC  
> Url: https://github.com/boostorg/math/pull/1044  

As reported by @robertramey on the ML

---

## Comment 1

> Username: mborland  
> Created_at: 2023-11-03 10:20:02 UTC  
> Url: https://github.com/boostorg/math/pull/1044#issuecomment-1792181649  

Errored on clone in drone. Merging.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2023-11-03 17:22:55 UTC  
> Url: https://github.com/boostorg/math/pull/1044#issuecomment-1792845211  

@mborland sorry to be late to this, I don't think this quite gets it - for one thing we have the same logic in round.hpp which also needs fixing, but the problem is more subtle - we have modern clang sitting on top of an ancient (pre-C++17) std library, so from the compilers perspective, everything that we're currently checking here is supported (or potentially so) where as the std library's <type_traits> has no C++17 `_v` suffixed constants.  Other than checking for a random C++17 header as a proxy in addition to the existing compiler checks I'm not sure what to suggest.  In the long run we could yet another macro to Boost.Config I guess... ah wait, I think this is `__cpp_lib_bool_constant >= 201505L`?  The latter requires inclusion of either type_traits or version, so the logic had probably better somewhere central.    
  
I'll see if I can put together a patch shortly, might be too late for this release though as I can see quite a few headers getting touched.

---

## Comment 3

> Username: mborland  
> Created_at: 2023-11-03 17:33:47 UTC  
> Url: https://github.com/boostorg/math/pull/1044#issuecomment-1792861007  

I thought clang didn't get a version of `__builtin_constant_evalutated` until 9? If the type_traits had compiled the ccmath ldexp would have errored on initializing a constexpr variable with a non-constexpr expression since it uses the STL at runtime. Unless someone is running say Clang-10 with libstdc++-5 shouldn't properly checking for `BOOST_MATH_NO_CONSTEXPR_DETECTION ` be sufficient? I think there's another week until RC release so you should have time.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2023-11-03 18:34:15 UTC  
> Url: https://github.com/boostorg/math/pull/1044#issuecomment-1792946799  

This is what I had in mind: https://github.com/boostorg/math/pull/1045  
  
And yes, from a practical point of view, you may be correct that no one would use clang-10 on top of gcc-5, though I'm constantly surprised by what what people do do!

---

## Comment 5

> Username: robertramey  
> Created_at: 2023-11-03 20:03:14 UTC  
> Url: https://github.com/boostorg/math/pull/1044#issuecomment-1793026235  

FYI. It's not just  mere "people".  It's Boost Developers.  Its from the test matrix which is our thing.

---
