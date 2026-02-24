# #275 - Wrong constant used [Closed]

> Username: grisumbras  
> Created at: 2025-07-18 13:09:09 UTC  
> Updated at: 2025-07-21 15:57:13 UTC  
> Closed at: 2025-07-21 15:57:13 UTC  
> Url: https://github.com/boostorg/charconv/issues/275  

While working on boostorg/json#993 I noticed that JSON benchmark performance when using `charconv::from_chars_erange` has degraded since the original release (by ~8% for canada.json and ~6% for numbers.json).  I've pinned down the degradation to 9b2e067. It seems that the reason is the log2 table, because I tried putting it in a cpp file and performance increased. Unfortunately, this couldn't be put in a PR, because the function using it is constexpr. Maybe you will find a solution.  
  
Anyway, while looking at the commit, I noticed that this line looks wrong: https://github.com/boostorg/charconv/blob/develop/include/boost/charconv/detail/from_chars_integer_impl.hpp#L225. The value should presumably be `std::numeric_limits<Integer>::digits`. If I understand it correctly, this bug can also cause performance degradation.

---

## Comment 1

> Username: mborland  
> Created at: 2025-07-18 13:15:45 UTC  
> Url: https://github.com/boostorg/charconv/issues/275#issuecomment-3089455334  

You're right about changing `digits10` to `digits`. Is it safe to assume your data is entirely in base10?

---

## Comment 2

> Username: grisumbras  
> Created at: 2025-07-18 13:23:24 UTC  
> Url: https://github.com/boostorg/charconv/issues/275#issuecomment-3089475183  

I actually don't use this function at all (I only use `from_chars_erange`). It seems the impact is purely indirect, from the code layout changes due to the table.

---

## Comment 3

> Username: mborland  
> Created at: 2025-07-18 14:19:32 UTC  
> Url: https://github.com/boostorg/charconv/issues/275#issuecomment-3089636900  

I padded the bottom of the table so that instead of 37 entries it has 64, and ran benchmarks before and after. Got ~5.5% improvement for `from_chars` for doubles which is inline with your observation.

---

## Comment 4

> Username: grisumbras  
> Created at: 2025-07-18 14:20:12 UTC  
> Url: https://github.com/boostorg/charconv/issues/275#issuecomment-3089639072  

I can test a PR with JSON's benchmark.

---

## Comment 5

> Username: mborland  
> Created at: 2025-07-18 14:21:16 UTC  
> Url: https://github.com/boostorg/charconv/issues/275#issuecomment-3089642599  

> I can test a PR with JSON's benchmark.  
  
See #276

---

## Comment 6

> Username: grisumbras  
> Created at: 2025-07-18 15:14:22 UTC  
> Url: https://github.com/boostorg/charconv/issues/275#issuecomment-3089817055  

To be honest, I don't see any improvement in JSON's benchmarks.

---

## Comment 7

> Username: mborland  
> Created at: 2025-07-18 18:42:55 UTC  
> Url: https://github.com/boostorg/charconv/issues/275#issuecomment-3090349984  

Can you try the linked PR again when you have a chance? I tried a different technique which is used all over the library.

---

## Comment 8

> Username: grisumbras  
> Created at: 2025-07-21 11:45:31 UTC  
> Url: https://github.com/boostorg/charconv/issues/275#issuecomment-3096402389  

Didn't help either. Let's just fix the constant for now.
