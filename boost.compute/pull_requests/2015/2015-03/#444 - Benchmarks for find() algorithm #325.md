# #444 Benchmarks for find() algorithm #325 [Merged]

> Username: jszuppe  
> Created at: 2015-03-26 13:01:36 UTC  
> Updated at: 2015-03-27 16:47:12 UTC  
> Merged at: 2015-03-27 15:17:36 UTC  
> Closed at: 2015-03-27 15:17:36 UTC  
> Url: https://github.com/boostorg/compute/pull/444  

This addresses issue #325 .  
  
I've added find() algorithm benchmarks for Boost.Compute, STL, Thrust. Benchmarks are based on worst-case scenario (searching for element that isn't in vector).

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-03-27 04:38:56 UTC  
> Updated_at: 2015-03-27 08:26:43 UTC  
> Url: https://github.com/boostorg/compute/pull/444#discussion_r27275651  

So I tried running this version a few times with varying data sizes but always got the same very small time. This usually means the compiler is optimizing out the call the `std::find()` entirely because the result is not actually used. I'd add something to store the result in a variable and compare it against `host_vector.end()`.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-03-27 04:41:38 UTC  
> Url: https://github.com/boostorg/compute/pull/444#issuecomment-86817363  

Minor issue with the STL version. After fixing that it should be good to merge. Thanks!

---

## Comment 3

> Username: jszuppe  
> Created_at: 2015-03-27 08:06:20 UTC  
> Updated_at: 2015-03-27 08:26:43 UTC  
> Url: https://github.com/boostorg/compute/pull/444#discussion_r27280305  

I don't have that issue. Are performance tests built with -O3 by default?   
  
Nevertheless, I will commit fix immediately and to be sure it won't happen again with Thrust performance test I will add there the same check.

---

## Comment 4

> Username: jszuppe  
> Created_at: 2015-03-27 09:18:13 UTC  
> Url: https://github.com/boostorg/compute/pull/444#issuecomment-86878160  

It's interesting that Travis build failed. I'm not sure why.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2015-03-27 15:14:52 UTC  
> Url: https://github.com/boostorg/compute/pull/444#discussion_r27302102  

Not by default, but when I'm running the performance benchmarks and comparing against the STL I enable optimizations (else the results are not very fair). I usually do this by setting `CMAKE_BUILD_TYPE=Release` which enables `gcc -03`.

---

## Comment 6

> Username: kylelutz  
> Created_at: 2015-03-27 15:17:48 UTC  
> Url: https://github.com/boostorg/compute/pull/444#issuecomment-86971634  

Looks great! Merged!

---

## Comment 7

> Username: kylelutz  
> Created_at: 2015-03-27 15:20:40 UTC  
> Url: https://github.com/boostorg/compute/pull/444#issuecomment-86972656  

And don't worry about the `g++-4.8: internal compiler error` Travis build error for GCC. It's not related to your change. I've been looking into it. For now, as long as the clang passes it should be good.

---

## Comment 8

> Username: jszuppe  
> Created_at: 2015-03-27 15:24:23 UTC  
> Url: https://github.com/boostorg/compute/pull/444#discussion_r27302861  

Thanks! That's exactly what I need to know. From now on I will use that setting when testing performance, especially against STL.

---
