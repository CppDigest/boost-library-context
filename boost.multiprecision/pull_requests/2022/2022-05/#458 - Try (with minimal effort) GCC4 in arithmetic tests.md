# #458 Try (with minimal effort) GCC4 in arithmetic tests [Merged]

> Username: ckormanyos  
> Created at: 2022-05-07 07:33:54 UTC  
> Updated at: 2022-05-11 06:57:07 UTC  
> Merged at: 2022-05-11 04:26:01 UTC  
> Closed at: 2022-05-11 04:26:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/458  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2022-05-08 12:13:21 UTC  
> Updated_at: 2022-05-08 12:14:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/458#issuecomment-1120407841  

The result of this PR so far is:  
  
- Do not include GCC 4.8 runs in CI.  
- Make a separate file `detail/check_cpp11_config.hpp` capable of local specialization if needed.  
- Repair numerous instances of mismatch `const`-versus-`constexpr`.  
- Repair consistency in all header guards according to use `BOOST_MP_`-plus-filename-`_HPP` and optionally add uniqueness (e.g. `_DETAIL`) as needed.  
  
Rational why I include the final point here is because I noticed this point with great clarity for the first time here.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2022-05-08 16:56:04 UTC  
> Updated_at: 2022-05-08 16:58:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/458#issuecomment-1120452124  

Hi John (@jzmaddock) this thing is review-ready. I'll struggle no further on it until we reach a 4.8 consensus. At the moment there is no effort to fully incorporate 4.8.  
  
This PR in this state officially closes #429. You can, however, also choose to pursue this thread further. I'm OK with that too. Or later get back on it, or whatever?  
  
I do, however, really want the changes within and as sketched/described above. Also a few changes in Math needed, as these are bugs anyway, I'll PR them shortly.  
  
If you get a chance, please at your convenience, tell me what you think...? And we can move on.  
  
Cc: @mborland and @NAThompson

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2022-05-10 14:29:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/458#issuecomment-1122474573  

Any feedback on this? It seems to be running green in CI.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2022-05-10 16:05:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/458#issuecomment-1122594742  

Sorry for the delay, yep, go for it!

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2022-05-11 05:36:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/458#issuecomment-1123206058  

>  yep, go for it!  
  
Thanks John. This PR ended up to not go entirely for GCC 4.8 and there is no 48. added run in CI. Multiprecision was pretty close to accepting 4.8, but a few small issues prevented that.  
  
GCC 4.8 did find some inconsistencies (mostly `const`-versus-`constexpr`) as mentioned above and this PR resolves these nicely.  
  
Cc: @mborland and @NAThompson

---
