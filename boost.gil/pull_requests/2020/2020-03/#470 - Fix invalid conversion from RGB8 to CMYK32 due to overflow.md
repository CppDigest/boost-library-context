# #470 Fix invalid conversion from RGB8 to CMYK32 due to overflow [Merged]

> Username: mloskot  
> Created at: 2020-03-31 00:55:52 UTC  
> Updated at: 2020-08-14 06:35:26 UTC  
> Merged at: 2020-04-10 17:11:33 UTC  
> Closed at: 2020-04-10 17:11:34 UTC  
> Url: https://github.com/boostorg/gil/pull/470  

### Description  
  
Correct calculation to correctly map CMYK 0 to minimum and 1 to maximum of input and output channel types.  
  
If float-point division is necessary, use double instead of float which may be too narrow for large operands. `cmyk32_pixel_t` is based on `uint32_t` which multiplication by its maximum may yield result too large to fit 32-bit float.  
  
For example, `(uint32_t(c) -  uint32_t(k)) * float(s)` for`c = 4294967295, k = 0, s = 1.0`  
results in `4294967300` value which does not fit `uint32_t`.  
  
**FIXME:** The original calculation was broken for signed CMYK target types. This has not been corrected yet, see #479  
  
### References  
  
- Fixes #406  
- Spawns #479  
- https://web.archive.org/web/20061119141350/www.neuro.sfc.keio.ac.jp/~aly/polygon/info/color-space-faq.html  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s) - ~currently there are no related tests, to be added~  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Commented]

> Username: simmplecoder  
> Created_at: 2020-03-31 06:49:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/470#pullrequestreview-384442629  

📁 include/boost/gil/color_convert.hpp

```diff
 160 |+         T2 const k_max = channel_traits<T2>::max_value();
 161 |+         T2 const s_div = k_max - k; // x = 1 - k
 162 |+         if (s_div > 0.0001f) // TODO: Where 0.0001 as close-to-zero test comes from?
```

> Username: simmplecoder  
> Created_at: 2020-03-31 06:46:53 UTC  
> Updated_at: 2020-04-09 00:00:37 UTC  
> Url: https://github.com/boostorg/gil/pull/470#discussion_r400678339  

I believe 0.0001 comes from the fact that multiplying by 255 will still be zero. Though I guess we could use `std::numeric_limits` to get the next representable number to 0.

> Username: mloskot  
> Created_at: 2020-03-31 21:08:59 UTC  
> Updated_at: 2020-04-09 00:00:37 UTC  
> Url: https://github.com/boostorg/gil/pull/470#discussion_r401216672  

The multiplication is by `1/255` but I think it is just reasonable not a hard Zero, so yes the `std::numeric_limits` should do too. Anyway, I won't change that in this PR as this PR is bare minimum to fix the bag (plus some formatting for saner readability of the code and less calls to the same).

---

📁 include/boost/gil/color_convert.hpp

```diff
 164 |+             if (k < k_max && s_div < k_max)
 165 |+             {
 166 |+                 float const s = k_max / float(s_div); // s = 1 / (1 - k)
```

> Username: simmplecoder  
> Created_at: 2020-03-31 06:48:55 UTC  
> Updated_at: 2020-04-09 00:00:37 UTC  
> Url: https://github.com/boostorg/gil/pull/470#discussion_r400679116  

It might make sense to choose to divide `s` or `k_max` by `s_div` depending on which is lower, because one will probably be smaller than the other, thus allowing us to at least decrease cases of overflow. Though that might lead to inaccuracies.

> Username: mloskot  
> Created_at: 2020-03-31 21:12:58 UTC  
> Updated_at: 2020-04-09 00:00:37 UTC  
> Url: https://github.com/boostorg/gil/pull/470#discussion_r401218578  

Yes, that could also affect range of output leading to some regressions.  
  
After the `if-else` restructuring, I don't see clear benefit of the division now as the overflow should not happen at all, I hope. Also, for white colours, if `k=0` e.g. then `s_div == k_max`.


---

## Comment 2

> Username: mloskot  
> Created_at: 2020-04-01 16:39:20 UTC  
> Updated_at: 2020-04-01 23:40:56 UTC  
> Url: https://github.com/boostorg/gil/pull/470#issuecomment-607359925  

The fix is still not correct as it does not convert well for negative values of signed pixel types like `rgb8s_pixel_t`. Use of signed integer for pixel components is extremely rare, but since the conversion is fairly type agnostic, it needs to be fixed.  
  
Basically, RGB color value `[-128, 127]` should be normalized to generic CMYK `[0, 1]`, then `0` should map to minimum and `1` to maximum of target channel type of `cmykN[s]_pixel_t`.

---

## Comment 3

> Username: mloskot  
> Created_at: 2020-04-10 17:10:51 UTC  
> Url: https://github.com/boostorg/gil/pull/470#issuecomment-612126068  

This just fixes the #406 and conversion issues to signed CMYK received separate issue #479

---
