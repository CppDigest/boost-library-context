# #233 Integrate PR for faster addition. [Closed]

> Username: jzmaddock  
> Created at: 2020-05-05 19:26:29 UTC  
> Updated at: 2021-01-30 16:10:18 UTC  
> Closed at: 2020-05-12 21:01:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/233  

Splits unsigned_add up into multiple compiler/processor specific parts with selection logic to choose between them.  
See https://github.com/boostorg/multiprecision/pull/231.

---

## Review 1 [Commented]

> Username: madhur4127  
> Created_at: 2020-05-06 04:33:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/233#pullrequestreview-406291188  

ADC_64, ADC_32 files, I think can be combined by switching based on a macro for appropriate versions?

📁 include/boost/multiprecision/cpp_int/addition/add_unsigned_addc_64.hpp

```diff
  49 |+       typename CppInt3::const_limb_pointer pb = b.limbs();
  50 |+       typename CppInt1::limb_pointer       pr = result.limbs();
  51 |+       //typename CppInt1::limb_pointer       pr_end = pr + m;
```

> Username: madhur4127  
> Created_at: 2020-05-06 04:02:17 UTC  
> Updated_at: 2020-05-09 10:22:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/233#discussion_r420533291  

this can be removed


📁 include/boost/multiprecision/cpp_int/addition/add_unsigned.hpp

```diff
  18 |+ #ifdef BOOST_MP_HAS_IMMINTRIN_H
  19 |+ 
  20 |+ #if defined(__AVX__) && defined(BOOST_HAS_INT128)
```

> Username: madhur4127  
> Created_at: 2020-05-06 04:30:36 UTC  
> Updated_at: 2020-05-09 10:22:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/233#discussion_r420539187  

As per intrinsics, I think this should be `__ADX__` ([link](https://clang.llvm.org/doxygen/adxintrin_8h_source.html))  
  
Also please see this before using ADX extensions: [link](https://stackoverflow.com/a/45796549/819979)

> Username: jzmaddock  
> Created_at: 2020-05-08 18:44:31 UTC  
> Updated_at: 2020-05-09 10:22:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/233#discussion_r422308621  

I've disabled AVX usage for gcc/clang since they don't generate AVX instructions anyway as you point out.  MSVC uses __AVX__ (see https://docs.microsoft.com/en-us/cpp/preprocessor/predefined-macros?view=vs-2019), just need to check Intel/linux.

> Username: madhur4127  
> Created_at: 2020-05-09 03:54:28 UTC  
> Updated_at: 2020-05-09 10:22:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/233#discussion_r422449467  

Intel IA32 Manual says to check CPUID bits for ADX extensions for ADCX, AODX.  
  
It would be amusing if MSVC defined ability of ADX in Advanced vector extension (AVX, SIMD). SIMD won't work because there's a carry dependency between limbs. AFAIK there's no preprocessor macro for ADX in MSVC.  
  
ADCX would be painful to made to work and would hurt performance in addition.  
  
ADX was originally designed to support ultra fast naive multiplication of 512*512 bits.

> Username: jzmaddock  
> Created_at: 2020-05-09 11:53:49 UTC  
> Updated_at: 2020-05-09 11:53:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/233#discussion_r422486988  

You're right! :(


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-05-08 17:59:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/233#issuecomment-625941244  

Ugh, getting this correctly configured sucks :(  
  
You're correct that gcc/clang don't support the adxc intrinsics, MSVC and Intel do so I've left the code in for them at present.... not convinced it makes much/any difference though.  It's a shame because I can see a use case for a double-addition-chain coming up.  
  
Changing to adding 64-bit chunks in msvc makes only a slight difference: we appear to be right at the limit here, as a straight memcpy is only very slightly cheaper than the addition routine for me in msvc!

---

## Comment 3

> Username: madhur4127  
> Created_at: 2020-05-09 16:11:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/233#issuecomment-626199017  

> Changing to adding 64-bit chunks in msvc makes only a slight difference: we appear to be right at the limit here, as a straight memcpy is only very slightly cheaper than the addition routine for me in msvc!  
  
Then writing a generic function instead of 32/64 version would remove code duplication. I am thinking of writing switches for selecting the versions (based on limb_type) and cast type (based on the compiler).

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2020-05-09 17:23:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/233#issuecomment-626208903  

> Then writing a generic function instead of 32/64 version would remove code duplication. I am thinking of writing switches for selecting the versions (based on limb_type) and cast type (based on the compiler).  
  
Nod.  That's not a bad idea now that we're starting to get a handle on what compiler supports what.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2020-05-12 21:01:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/233#issuecomment-627591513  

Superseded by #234.

---
