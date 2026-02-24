# #548 - The future of fpclassify.hpp [Open]

> Username: jzmaddock  
> Created at: 2021-02-21 18:26:33 UTC  
> Updated at: 2021-02-21 22:55:53 UTC  
> Url: https://github.com/boostorg/math/issues/548  

I'm opening this as a place for discussion really.  
  
We currently have boost::math::fpclassify/isnan/isinf/isnormal etc which began life as workarounds for C++03 compilers, but they have another role: for IEEE conforming types they work even in the presence of -ffast-math.  
  
Now, I have some dim recollection, that gcc/clang used to replace say `std::isnan(x)` with a literal `false` even with just -O3. And that we had some very inscrutable bug reports caused by this.  However, that appears not to be the case with current releases, so perhaps this has been fixed/changed?  
  
So... I'm undecided whether the boost functions should become synonyms for the std ones, but just wanted to flag the issue.

---

## Comment 1

> Username: NAThompson  
> Created at: 2021-02-21 22:50:48 UTC  
> Updated at: 2021-02-21 22:55:53 UTC  
> Url: https://github.com/boostorg/math/issues/548#issuecomment-782942724  

Yeah replacing `std::isnan(x)` with `false` is the *worst*!  
  
[Here's](https://godbolt.org/z/cjTG8Y) a godbolt to play around with the compiler flags.  
  
Summarizing:  
  
- gcc 10.2: `-O3 -ffast-math` -> `std::isnan(x)` always is false. However, the flag is `-O3 -ffast-math -fno-finite-math-only` gets the correct behavior.  
- `icc` appears to get it right no matter what the flag is.  
- `clang 11` does the "wrong" thing with `-O3 -ffast-math`, and does the "right" thing with `-O3` as well as `-O3 -ffast-math -fno-finite-math-only` .
