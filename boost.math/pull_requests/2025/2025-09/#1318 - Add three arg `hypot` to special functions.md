# #1318 Add three arg `hypot` to special functions [Merged]

> Username: mborland  
> Created at: 2025-09-08 11:52:53 UTC  
> Updated at: 2025-09-09 09:37:39 UTC  
> Merged at: 2025-09-09 09:37:35 UTC  
> Closed at: 2025-09-09 09:37:35 UTC  
> Url: https://github.com/boostorg/math/pull/1318  

Closes: #1114

---

## Comment 1

> Username: mborland  
> Created_at: 2025-09-08 13:27:02 UTC  
> Updated_at: 2025-09-08 13:27:34 UTC  
> Url: https://github.com/boostorg/math/pull/1318#issuecomment-3266314028  

Only CI failure on this is failure to clone. Any comments @ckormanyos or @jzmaddock? My one big assumption to make the SFINAE work for hypot(x, y, pol) vs hypot(x, y, z) is that the numeric types always have specializations for `std::numeric_limits`, since `std::numeric_limits<policy>::is_specialized` defaults to false. Should also work fine on GPU because it is using the special `boost::math::` overloads instead of `std::`

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-09-08 14:14:31 UTC  
> Url: https://github.com/boostorg/math/pull/1318#issuecomment-3266544061  

> Any comments  
  
It seems OK to me (although I admit, I've never used the 3-argument `hypot` function myself).

---

## Comment 3

> Username: mborland  
> Created_at: 2025-09-08 14:21:05 UTC  
> Url: https://github.com/boostorg/math/pull/1318#issuecomment-3266569081  

> > Any comments  
>   
> It seems OK to me (although I admit, I've never used the 3-argument `hypot` function myself).  
  
A legitimate user asked on Friday with the linked issue so I took care of it since it's simple.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2025-09-08 16:29:54 UTC  
> Url: https://github.com/boostorg/math/pull/1318#issuecomment-3267078845  

Thanks Matt!  
  
Some general comment:  
  
* For the sake of consistency, we should probably use argument promotion inside the body of the outer forwarding function.  
* These should be tested by `real_concept` and also added to test/compile_test/instantiate.hpp (look for all the occurrences of `hypot` in there).  These will likely throw up all kinds of obscure issues (sorry!!), not least that we don't require `numeric_limits` to be specialized - this is because variable precision types can't meaningfully provide a specialization.  BTW There is a `policies::is_policy` trait for Policy detection - but the existing code that uses it is all old C++03 era, and convoluted, so doesn't make good examples I'm afraid!

---

## Comment 5

> Username: mborland  
> Created_at: 2025-09-09 06:35:19 UTC  
> Url: https://github.com/boostorg/math/pull/1318#issuecomment-3269089015  

Thanks @jzmaddock. I think I hit everything. Fortunately the compile tests and instantiate went fine (locally) since I copied the outer functions from 2-arg. With a slight adjustment to `is_policy`, it works well for SFINAE.

---

## Comment 6

> Username: mborland  
> Created_at: 2025-09-09 09:37:25 UTC  
> Url: https://github.com/boostorg/math/pull/1318#issuecomment-3269791588  

As usual the two failures in Drone are runner issues. Merging

---
