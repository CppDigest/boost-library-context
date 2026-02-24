# #916 Remove most CI and just test arm64/drone. [Merged]

> Username: jzmaddock  
> Created at: 2023-01-11 18:37:56 UTC  
> Updated at: 2024-01-24 21:33:50 UTC  
> Merged at: 2023-01-14 09:24:46 UTC  
> Closed at: 2023-01-14 09:24:46 UTC  
> Url: https://github.com/boostorg/math/pull/916  

!!REVERT THIS COMMIT BEFORE MERGE!!

---

## Comment 1

> Username: mborland  
> Created_at: 2023-01-12 01:05:48 UTC  
> Url: https://github.com/boostorg/math/pull/916#issuecomment-1379678300  

I'm surprised this is green without any corrections

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2023-01-12 11:42:40 UTC  
> Url: https://github.com/boostorg/math/pull/916#issuecomment-1380204573  

@mborland : Me too!  But I'm not particularly unhappy about it :) There were a heck of a lot of fixes in s390x testing though which seems to have done the trick.  Testing a full run now....

---

## Comment 3

> Username: mborland  
> Created_at: 2023-01-12 17:01:41 UTC  
> Url: https://github.com/boostorg/math/pull/916#issuecomment-1380727606  

Looking at the drone build logs I think we need to revisit how much we are testing with these emulated architectures. Our median build time prior to merging s390x was 46 minutes. Our recent tests have been taking between 5-15 hours because the number of runners for s390x is limited. I recommend we test these like the sanitizers; pick one language standard (C++17) and that is it. We shouldn't need to test `__float128` using GNU extensions on s390x because long double is 128-bits, and ARM does not support libquadmath.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2023-01-12 17:54:43 UTC  
> Url: https://github.com/boostorg/math/pull/916#issuecomment-1380787384  

All good thoughts, once this is green I'll cut down the permutations.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2023-01-14 09:24:37 UTC  
> Url: https://github.com/boostorg/math/pull/916#issuecomment-1382699503  

Failures are spurious, merging.

---
