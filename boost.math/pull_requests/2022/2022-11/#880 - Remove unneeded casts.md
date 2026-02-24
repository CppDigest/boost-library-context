# #880 Remove unneeded casts [Closed]

> Username: SiliconA-Z  
> Created at: 2022-11-15 17:38:00 UTC  
> Updated at: 2023-10-17 19:29:18 UTC  
> Closed at: 2023-10-17 16:11:34 UTC  
> Url: https://github.com/boostorg/math/pull/880  

Most of these casts are redundant, and automatically dealt with by either the C++/C standard, or operations done onto said numbers, such as & 1 operation making casts to int pointless.

---

## Comment 1

> Username: SiliconA-Z  
> Created_at: 2022-11-30 15:44:26 UTC  
> Url: https://github.com/boostorg/math/pull/880#issuecomment-1332368667  

Any update on this? @mborland

---

## Comment 2

> Username: mborland  
> Created_at: 2022-11-30 15:52:26 UTC  
> Url: https://github.com/boostorg/math/pull/880#issuecomment-1332379874  

> Any update on this? @mborland  
  
I don't think this passes the litmus test I have given you for inclusion. You also just pushed updates 10 mins ago?

---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2022-12-01 17:24:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/880#pullrequestreview-1201401682  

📁 include/boost/math/cstdfloat/cstdfloat_iostream.hpp

```diff
 450 | 
 451 |-       if(-expon > std::numeric_limits<float_type>::max_exponent10 - 3)
 451 |+       if(expon < -std::numeric_limits<float_type>::max_exponent10 + 3)
```

> Username: jzmaddock  
> Created_at: 2022-12-01 17:24:22 UTC  
> Url: https://github.com/boostorg/math/pull/880#discussion_r1037383973  

Why this change?   What if max_exponent10 is so large that adding 3 overflows?

> Username: SiliconA-Z  
> Created_at: 2023-09-30 15:31:08 UTC  
> Url: https://github.com/boostorg/math/pull/880#discussion_r1341974786  

Fixed!


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2022-12-01 17:27:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/880#pullrequestreview-1201406636  

📁 include/boost/math/cstdfloat/cstdfloat_iostream.hpp

```diff
 640 | 
 642 |-       while(std::isdigit(*p))
 641 |+       while(std::isdigit(*p) && digits_seen <= max_digits)
```

> Username: jzmaddock  
> Created_at: 2022-12-01 17:27:55 UTC  
> Updated_at: 2022-12-01 17:27:56 UTC  
> Url: https://github.com/boostorg/math/pull/880#discussion_r1037387393  

I believe this changes semantics?

> Username: SiliconA-Z  
> Created_at: 2023-09-30 15:31:05 UTC  
> Url: https://github.com/boostorg/math/pull/880#discussion_r1341974776  

Fixed


---

## Comment 5

> Username: SiliconA-Z  
> Created_at: 2023-09-30 15:39:08 UTC  
> Url: https://github.com/boostorg/math/pull/880#issuecomment-1741792788  

@mborland Addressed!

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2023-10-17 16:11:34 UTC  
> Url: https://github.com/boostorg/math/pull/880#issuecomment-1766737211  

Closing in favour of https://github.com/boostorg/math/pull/1039.  
The code being changed turned out to have no associated tests, so those are now added there.

---
