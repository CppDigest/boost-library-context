# #993 Disable numeric_limits specialization for GCC-14 [Merged]

> Username: mborland  
> Created at: 2023-06-07 13:45:58 UTC  
> Updated at: 2023-12-12 16:34:45 UTC  
> Merged at: 2023-06-08 07:08:52 UTC  
> Closed at: 2023-06-08 07:08:52 UTC  
> Url: https://github.com/boostorg/math/pull/993  

Closes: #992

---

## Comment 1

> Username: mborland  
> Created_at: 2023-06-07 15:02:10 UTC  
> Url: https://github.com/boostorg/math/pull/993#issuecomment-1581009514  

I built GCC from source, `test_cstdfloat` and the minimal reproducer from the example both pass fine with this PR.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2023-06-07 15:46:51 UTC  
> Url: https://github.com/boostorg/math/pull/993#issuecomment-1581095893  

Just checking.... does libstdc++3 <limits> encase `numeric_limits<__float128>` inside a #if?  They often do, in which case it might be better to check for whatever internal macro they are using rather than assuming gcc-14 unconditionally supports this.

---

## Comment 3

> Username: mborland  
> Created_at: 2023-06-07 15:53:33 UTC  
> Updated_at: 2023-06-07 15:53:56 UTC  
> Url: https://github.com/boostorg/math/pull/993#issuecomment-1581107046  

It does have an `#if` block, and the diff [is here](https://gcc.gnu.org/git/?p=gcc.git;a=blobdiff;f=libstdc%2B%2B-v3/include/std/limits;h=5f341e63b931693022d847c17baff3214dee3269;hp=8bafd6fb9724aefe43cdf7a840dac2bb8c6d7e73;hb=f150a084e25;hpb=51cf0b3949b88b5e622872e4dac019ceb27400e1). The two macros they use are already wrapped into defining `BOOST_MATH_USE_FLOAT128`.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2023-06-07 17:22:12 UTC  
> Url: https://github.com/boostorg/math/pull/993#issuecomment-1581232557  

Ah, nothing useful there then, this all looks good to me then.

---

## Review 5 [Commented]

> Username: jwakely  
> Created_at: 2023-12-12 16:28:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/993#pullrequestreview-1777950659  

📁 include/boost/math/cstdfloat/cstdfloat_limits.hpp

```diff
  26 | 
  27 |-   #if defined(BOOST_CSTDFLOAT_HAS_INTERNAL_FLOAT128_T) && defined(BOOST_MATH_USE_FLOAT128) && !defined(BOOST_CSTDFLOAT_NO_LIBQUADMATH_SUPPORT)
  27 |+   #if defined(BOOST_CSTDFLOAT_HAS_INTERNAL_FLOAT128_T) && defined(BOOST_MATH_USE_FLOAT128) && !defined(BOOST_CSTDFLOAT_NO_LIBQUADMATH_SUPPORT) && (!defined(__GNUC__) || (defined(__GNUC__) && __GNUC__ < 14))
```

> Username: jwakely  
> Created_at: 2023-12-12 16:28:49 UTC  
> Updated_at: 2023-12-12 16:28:50 UTC  
> Url: https://github.com/boostorg/math/pull/993#discussion_r1424269220  

This check seems wrong. The new `numeric_limits<__float128>` specialization will be enabled when using new libstdc++ headers with Clang, which does not define `__GNUC__` to 14 (it can be told to use any value, but by default it defines it to 4).

> Username: jwakely  
> Created_at: 2023-12-12 16:30:40 UTC  
> Url: https://github.com/boostorg/math/pull/993#discussion_r1424272069  

Checking `_GLIBCXX_RELEASE < 14` would be more correct, as that is determined by the libstdc++ headers, not by the compiler including them.


---

## Comment 6

> Username: jwakely  
> Created_at: 2023-12-12 16:34:37 UTC  
> Url: https://github.com/boostorg/math/pull/993#issuecomment-1852396903  

> It does have an `#if` block, and the diff [is here](https://gcc.gnu.org/git/?p=gcc.git;a=blobdiff;f=libstdc%2B%2B-v3/include/std/limits;h=5f341e63b931693022d847c17baff3214dee3269;hp=8bafd6fb9724aefe43cdf7a840dac2bb8c6d7e73;hb=f150a084e25;hpb=51cf0b3949b88b5e622872e4dac019ceb27400e1). The two macros they use are already wrapped into defining `BOOST_MATH_USE_FLOAT128`.  
  
FWIW the current code uses slightly different conditions:  
https://gcc.gnu.org/git/?p=gcc.git;a=blob;f=libstdc%2B%2B-v3/include/std/limits;h=ec0b7a1ca7bf38b7440b6e94c692cd5937f1bf23;hb=HEAD#l2084  
  
That was changed by https://gcc.gnu.org/git/?p=gcc.git;a=commitdiff;h=6261d10521f9fdc2a43d54b4dc365025288aa8e9

---
