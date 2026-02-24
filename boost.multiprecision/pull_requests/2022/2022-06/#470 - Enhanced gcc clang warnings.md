# #470 Enhanced gcc clang warnings [Merged]

> Username: ckormanyos  
> Created at: 2022-06-05 13:45:54 UTC  
> Updated at: 2023-01-17 20:52:09 UTC  
> Merged at: 2022-06-06 10:26:04 UTC  
> Closed at: 2022-06-06 10:26:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/470  

This PR attempts to handle the remaining conversion warnings on the MPFR backend. This will wrap up the effort to handle enhanced GCC/clang/MSVC warnings in this trimester, as the other backends are finished.  
  
Some remaining work is probably lurking around, undiscovered, in various less-well-used parts of Multiprecision. The entire warning-hardening drive, however, has resulted in hundreds of edits and running tests.  
  
Let's see how this goes in CI...?  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2022-06-05 17:34:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/470#pullrequestreview-996025556  

📁 include/boost/multiprecision/cpp_dec_float.hpp

```diff
 386 |+       static constexpr unsigned     bit_shift = sizeof(unsigned long long) * CHAR_BIT;
 387 |       static constexpr uint128_type mask      = (static_cast<uint128_type>(1u) << bit_shift) - 1;
 387 |-       unsigned                           shift     = bit_shift;
```

> Username: jzmaddock  
> Created_at: 2022-06-05 17:34:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/470#discussion_r889716607  

That must have been a cut-and-paste error, variable shift is unused and can be deleted rather than cast to void.  
  
Other than that, this all looks great to me!

> Username: ckormanyos  
> Created_at: 2022-06-06 10:25:23 UTC  
> Updated_at: 2022-06-06 10:25:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/470#discussion_r890017880  

I forgot this in my recent push to green. I am going to commit and merge to develop anyway and cycle back to this trivial removal later.


---

## Comment 2

> Username: ckormanyos  
> Created_at: 2022-06-05 17:47:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/470#issuecomment-1146855632  

> Other than that, this all looks great to me!  
  
I has thought so also, ... until I, oops, used too much of 14...  
  
Oops. I got so pumped up on C++14, that I ended up using a couple of instantiations of `std::make_unsigned_t<>`. Of course this is not yet allowed since we still test on C++11.  
  
So I will need to remove these tomorrow.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2022-06-06 10:25:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/470#issuecomment-1147297063  

> ended up using a couple of instantiations of `std::make_unsigned_t<>`  
  
> ... will need to remove these tomorrow.  
  
Done. Green

---
