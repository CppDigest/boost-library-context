# #1009 Fix assertions and fenv errors in beta distribution [Closed]

> Username: mborland  
> Created at: 2023-07-31 18:31:16 UTC  
> Updated at: 2024-01-24 21:33:46 UTC  
> Closed at: 2023-09-02 08:15:30 UTC  
> Url: https://github.com/boostorg/math/pull/1009  

I was able to reproduce the error in python using the script from #1006. The assertion was not triggered using the same template parameters in C++, but the fenv flags are being set. Below is the diagnostic data from `BOOST_MATH_INSTRUMENT`  
  
```  
====== BEGIN OUTPUT ======  
lgamma_imp called with e N5boost4math7lanczos12lanczos17m64E  
../../../boost/math/special_functions/erf.hpp:1226 result_type = e  
../../../boost/math/special_functions/erf.hpp:1227 value_type = e  
../../../boost/math/special_functions/erf.hpp:1228 precision_type = N5boost4math8policies7digits2ILi64EEE  
../../../boost/math/special_functions/erf.hpp:1237 tag_type = St17integral_constantIiLi64EE  
../../../boost/math/special_functions/erf.hpp:427 64-bit precision erf_imp called  
../../../boost/math/special_functions/erf.hpp:1191 result_type = e  
../../../boost/math/special_functions/erf.hpp:1192 value_type = e  
../../../boost/math/special_functions/erf.hpp:1193 precision_type = N5boost4math8policies7digits2ILi64EEE  
../../../boost/math/special_functions/erf.hpp:1202 tag_type = St17integral_constantIiLi64EE  
../../../boost/math/special_functions/erf.hpp:427 64-bit precision erf_imp called  
../../../boost/math/special_functions/erf.hpp:1191 result_type = e  
../../../boost/math/special_functions/erf.hpp:1192 value_type = e  
../../../boost/math/special_functions/erf.hpp:1193 precision_type = N5boost4math8policies7digits2ILi64EEE  
../../../boost/math/special_functions/erf.hpp:1202 tag_type = St17integral_constantIiLi64EE  
../../../boost/math/special_functions/erf.hpp:427 64-bit precision erf_imp called  
../../../boost/math/special_functions/erf.hpp:1191 result_type = e  
../../../boost/math/special_functions/erf.hpp:1192 value_type = e  
../../../boost/math/special_functions/erf.hpp:1193 precision_type = N5boost4math8policies7digits2ILi64EEE  
../../../boost/math/special_functions/erf.hpp:1202 tag_type = St17integral_constantIiLi64EE  
../../../boost/math/special_functions/erf.hpp:427 64-bit precision erf_imp called  
../../../boost/math/special_functions/erf.hpp:1191 result_type = e  
../../../boost/math/special_functions/erf.hpp:1192 value_type = e  
../../../boost/math/special_functions/erf.hpp:1193 precision_type = N5boost4math8policies7digits2ILi64EEE  
../../../boost/math/special_functions/erf.hpp:1202 tag_type = St17integral_constantIiLi64EE  
../../../boost/math/special_functions/erf.hpp:427 64-bit precision erf_imp called  
../../../boost/math/special_functions/erf.hpp:1191 result_type = e  
../../../boost/math/special_functions/erf.hpp:1192 value_type = e  
../../../boost/math/special_functions/erf.hpp:1193 precision_type = N5boost4math8policies7digits2ILi64EEE  
../../../boost/math/special_functions/erf.hpp:1202 tag_type = St17integral_constantIiLi64EE  
../../../boost/math/special_functions/erf.hpp:427 64-bit precision erf_imp called  
../../../boost/math/special_functions/erf.hpp:1191 result_type = e  
../../../boost/math/special_functions/erf.hpp:1192 value_type = e  
../../../boost/math/special_functions/erf.hpp:1193 precision_type = N5boost4math8policies7digits2ILi64EEE  
../../../boost/math/special_functions/erf.hpp:1202 tag_type = St17integral_constantIiLi64EE  
../../../boost/math/special_functions/erf.hpp:427 64-bit precision erf_imp called  
Second order root iteration, guess = 3.3621031431120935062626778173217526e-4932, min = 3.3621031431120935062626778173217526e-4932, max = 1, digits = 48, max_iter = 200  
Second order root iteration, limit = 7.10542735760100185871124267578125e-15  
../../../boost/math/special_functions/beta.hpp:1011 a = 9.99999999999999909326625337248462e-309  
../../../boost/math/special_functions/beta.hpp:1012 b = 5  
../../../boost/math/special_functions/beta.hpp:1013 x = 3.3621031431120935062626778173217526e-4932  
../../../boost/math/special_functions/beta.hpp:1014 inv = 0  
../../../boost/math/special_functions/beta.hpp:1015 normalised = 1  
../../../boost/math/special_functions/beta.hpp:745 k = 20  
../../../boost/math/special_functions/beta.hpp:264 result = 9.99999999999999909326625337248462e-309  
../../../boost/math/special_functions/beta.hpp:279 result = 9.99999999999999909326625337248462e-309  
../../../boost/math/special_functions/beta.hpp:417 result = 9.99999999999999909326625337248462e-309  
../../../boost/math/special_functions/beta.hpp:1263 fract = 1.6926248444201733823383717339722608e-308  
tgamma_imp called with e N5boost4math7lanczos12lanczos17m64E  
../../../boost/math/special_functions/gamma.hpp:153 result = 1  
../../../boost/math/special_functions/gamma.hpp:758 -5.7721566490153280829063366506040647e-309  
../../../boost/math/special_functions/beta.hpp:1274 fract = 1  
../../../boost/math/tools/roots.hpp:554 f0 = 0.7999999999999999888977697537484346  
../../../boost/math/tools/roots.hpp:555 f1 = 2.9743287383930791430219742500652233e+4623  
../../../boost/math/tools/roots.hpp:556 f2 = 2.9743287383930791430219742500652233e+4623  
../../../boost/math/tools/roots.hpp:347 denom = 1.5999999999999999777955395074968692  
../../../boost/math/tools/roots.hpp:348 num = 5.9486574767861582860439485001304467e+4623  
Second order root iteration, delta = 2.6896825144896750115355219142178362e-4624, residual = 0.7999999999999999888977697537484346  
../../../boost/math/tools/roots.hpp:617 result = -2.6896825144896750115355219142178362e-4624  
Second order root finder required 2 iterations.  
FE_UNDERFLOW  
Error at git_issue_1006.cpp:show_fpexcept_flags:39  
  Condition '1 == 0' is not satisfied:  
  Expected: 1 = 0x1  
  Computed: 0 = 0x0  
Second order root iteration, guess = 3.3621031431120935062626778173217526e-4932, min = 3.3621031431120935062626778173217526e-4932, max = 1, digits = 48, max_iter = 200  
Second order root iteration, limit = 7.10542735760100185871124267578125e-15  
../../../boost/math/special_functions/beta.hpp:1011 a = 1.0000000000000018855892087022346387e-309  
../../../boost/math/special_functions/beta.hpp:1012 b = 5  
../../../boost/math/special_functions/beta.hpp:1013 x = 3.3621031431120935062626778173217526e-4932  
../../../boost/math/special_functions/beta.hpp:1014 inv = 0  
../../../boost/math/special_functions/beta.hpp:1015 normalised = 1  
../../../boost/math/special_functions/beta.hpp:745 k = 20  
../../../boost/math/special_functions/beta.hpp:264 result = 1.0000000000000018855892087022346387e-309  
../../../boost/math/special_functions/beta.hpp:279 result = 1.0000000000000018855892087022346387e-309  
../../../boost/math/special_functions/beta.hpp:417 result = 1.0000000000000018855892087022346387e-309  
../../../boost/math/special_functions/beta.hpp:1263 fract = 1.6926248444201767274739975377734781e-309  
../../../boost/math/special_functions/gamma.hpp:153 result = 1  
../../../boost/math/special_functions/gamma.hpp:758 -5.7721566490153394900057839596946173e-310  
../../../boost/math/special_functions/beta.hpp:1274 fract = 1  
../../../boost/math/tools/roots.hpp:554 f0 = 0.7999999999999999888977697537484346  
../../../boost/math/tools/roots.hpp:555 f1 = 2.9743287383930850210765705634924873e+4622  
../../../boost/math/tools/roots.hpp:556 f2 = 2.9743287383930850210765705634924873e+4622  
../../../boost/math/tools/roots.hpp:347 denom = 1.5999999999999999777955395074968692  
../../../boost/math/tools/roots.hpp:348 num = 5.9486574767861700421531411269849746e+4622  
Second order root iteration, delta = 2.6896825144896696959789295395794399e-4623, residual = 0.7999999999999999888977697537484346  
../../../boost/math/tools/roots.hpp:617 result = -2.6896825144896696959789295395794399e-4623  
Second order root finder required 2 iterations.  
FE_UNDERFLOW  
Error at git_issue_1006.cpp:show_fpexcept_flags:39  
  Condition '1 == 0' is not satisfied:  
  Expected: 1 = 0x1  
  Computed: 0 = 0x0  
Second order root iteration, guess = 3.3621031431120935062626778173217526e-4932, min = 3.3621031431120935062626778173217526e-4932, max = 1, digits = 48, max_iter = 200  
Second order root iteration, limit = 7.10542735760100185871124267578125e-15  
../../../boost/math/special_functions/beta.hpp:1011 a = 9.8813129168249308835313758573644274e-324  
../../../boost/math/special_functions/beta.hpp:1012 b = 5  
../../../boost/math/special_functions/beta.hpp:1013 x = 3.3621031431120935062626778173217526e-4932  
../../../boost/math/special_functions/beta.hpp:1014 inv = 0  
../../../boost/math/special_functions/beta.hpp:1015 normalised = 1  
../../../boost/math/special_functions/beta.hpp:745 k = 20  
../../../boost/math/special_functions/beta.hpp:264 result = 9.8813129168249308824600417642213608e-324  
../../../boost/math/special_functions/beta.hpp:279 result = 9.8813129168249308824600417642213608e-324  
../../../boost/math/special_functions/beta.hpp:417 result = 9.8813129168249308824600417642213608e-324  
../../../boost/math/special_functions/beta.hpp:1263 fract = 1.6725355738507849797162009039207305e-323  
../../../boost/math/special_functions/gamma.hpp:153 result = 1  
../../../boost/math/special_functions/gamma.hpp:758 -5.703648605385207552241717693489347e-324  
../../../boost/math/special_functions/beta.hpp:1274 fract = 1  
../../../boost/math/tools/roots.hpp:554 f0 = 0.7999999999999999888977697537484346  
../../../boost/math/tools/roots.hpp:555 f1 = 2.9390272981567136316757344407503151e+4608  
../../../boost/math/tools/roots.hpp:556 f2 = 2.9390272981567136316757344407503151e+4608  
../../../boost/math/tools/roots.hpp:347 denom = 1.5999999999999999777955395074968692  
../../../boost/math/tools/roots.hpp:348 num = 5.8780545963134272633514688815006302e+4608  
Second order root iteration, delta = 2.721989008069916574949237277740126e-4609, residual = 0.7999999999999999888977697537484346  
../../../boost/math/tools/roots.hpp:617 result = -2.721989008069916574949237277740126e-4609  
Second order root finder required 2 iterations.  
FE_UNDERFLOW  
Error at git_issue_1006.cpp:show_fpexcept_flags:39  
  Condition '1 == 0' is not satisfied:  
  Expected: 1 = 0x1  
  Computed: 0 = 0x0  
Error count: 3  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
```

---

## Comment 1

> Username: mborland  
> Created_at: 2023-07-31 19:03:29 UTC  
> Url: https://github.com/boostorg/math/pull/1009#issuecomment-1658975097  

On Apple M1 the python trace does not hit the assertion but gives the same overflow error in both cases:  
  
```  
[[5e-324, inf], [5e-324, inf]]  
2.225073858507201e-308  
  
Traceback (most recent call last):  
  File "/Users/mborland/Documents/boost/libs/math/test/git_issue_1006.py", line 6, in <module>  
    print(scipy.stats.beta(a=1e-309, b=5).ppf(.2))  
          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  
  File "/opt/homebrew/lib/python3.11/site-packages/scipy/stats/_distn_infrastructure.py", line 483, in ppf  
    return self.dist.ppf(q, *self.args, **self.kwds)  
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  
  File "/opt/homebrew/lib/python3.11/site-packages/scipy/stats/_distn_infrastructure.py", line 2249, in ppf  
    place(output, cond, self._ppf(*goodargs) * scale + loc)  
                        ^^^^^^^^^^^^^^^^^^^^  
  File "/opt/homebrew/lib/python3.11/site-packages/scipy/stats/_continuous_distns.py", line 701, in _ppf  
    return _boost._beta_ppf(q, a, b)  
           ^^^^^^^^^^^^^^^^^^^^^^^^^  
OverflowError: Error in function boost::math::beta<d>(%1%,%1%):  
  
Traceback (most recent call last):  
  File "/Users/mborland/Documents/boost/libs/math/test/git_issue_1006.py", line 9, in <module>  
    print(scipy.stats.beta(a=1e-323, b=5).ppf(.2))  
          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  
  File "/opt/homebrew/lib/python3.11/site-packages/scipy/stats/_distn_infrastructure.py", line 483, in ppf  
    return self.dist.ppf(q, *self.args, **self.kwds)  
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  
  File "/opt/homebrew/lib/python3.11/site-packages/scipy/stats/_distn_infrastructure.py", line 2249, in ppf  
    place(output, cond, self._ppf(*goodargs) * scale + loc)  
                        ^^^^^^^^^^^^^^^^^^^^  
  File "/opt/homebrew/lib/python3.11/site-packages/scipy/stats/_continuous_distns.py", line 701, in _ppf  
    return _boost._beta_ppf(q, a, b)  
           ^^^^^^^^^^^^^^^^^^^^^^^^^  
OverflowError: Error in function boost::math::beta<d>(%1%,%1%):  
```

---

## Comment 2

> Username: mdhaber  
> Created_at: 2023-08-20 08:01:01 UTC  
> Url: https://github.com/boostorg/math/pull/1009#issuecomment-1685218295  

Hey @mborland thought I'd check in about this one. Looks like it hit a snag? Should we add a workaround in the meantime?

---

## Comment 3

> Username: mborland  
> Created_at: 2023-08-22 12:17:41 UTC  
> Url: https://github.com/boostorg/math/pull/1009#issuecomment-1688074162  

I am going to defer to @jzmaddock on this one since he found additional issues with the beta implementation in a [different PR](https://github.com/boostorg/math/pull/1007). I'd rather not break things he's already looking at.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2023-08-31 17:55:19 UTC  
> Url: https://github.com/boostorg/math/pull/1009#issuecomment-1701511702  

Thanks Matt, just looking at this now, and struggling to reproduce with MSVC: like you I see some exception flags being set, but the line mentioned in the assertion is never called much less triggered :(

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2023-08-31 17:57:13 UTC  
> Url: https://github.com/boostorg/math/pull/1009#issuecomment-1701514229  

Also this fixed some issues in this area: https://github.com/boostorg/math/issues/961

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2023-09-02 08:15:30 UTC  
> Url: https://github.com/boostorg/math/pull/1009#issuecomment-1703761542  

Closed via https://github.com/boostorg/math/pull/1026

---
