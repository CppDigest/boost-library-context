# #445 - Harden code with higher GCC warnings [Closed]

> Username: ckormanyos  
> Created at: 2022-04-06 13:22:46 UTC  
> Updated at: 2022-06-06 10:26:05 UTC  
> Closed at: 2022-06-06 10:26:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/445  

The purpose of this issue is to raise awarenes for possible code hardening via use of stricter/higher GCC warnings.  
  
Consider starting with the set:  
  
```sh  
g++ -Wall -Wconversion -Wextra -Wpedantic -Wshadow -Wundef  
```  
  
Further details such as potential CI job(s), how to best detect warning instances, etc. are partly still TODO.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2022-04-12 06:29:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/445#issuecomment-1096157612  

First evaluation reveals there is expected to be problems using `-Wpedantic` in association with GCC's non-ANSI data type `__int128`. Either `-Wpedantic` is not appropriate, or some kind of commenting out warnings is needed (which is not pretty because the library actually uses 128 bit types).

---

## Comment 2

> Username: ckormanyos  
> Created at: 2022-04-12 06:38:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/445#issuecomment-1096169963  

A good starting point might be the following (on GCC/clang)  
  
```  
-Wall -Wextra -Wconversion -Wsign-conversion -Wshadow -Wundef  
```

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-04-12 07:51:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/445#issuecomment-1096289519  

If we always use __int128 via a typedef marked with __extension__ (cf __float128 usage), does that fix things?

---

## Comment 4

> Username: ckormanyos  
> Created at: 2022-04-12 07:55:27 UTC  
> Updated at: 2022-04-12 07:56:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/445#issuecomment-1096296236  

> If we always use `__int128` via a typedef marked with **extension** (cf __float128 usage), does that fix things?  
  
Good questioon John (@jzmaddock). That will be part of this investigation/hardening step. We will attempt to find out if there is a way to secure the reliable use of both `__int128` as well as `__float128` in the presence of `-Wpedantic` --- and do so without requiring excessive amounts of code-modification.  
  
I'll look into this as part of this endeavor.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2022-04-21 13:27:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/445#issuecomment-1105205329  

So I started the ball rolling on this in a new [enhanced_gcc_clang_warnings](https://github.com/boostorg/multiprecision/tree/enhanced_gcc_clang_warnings) branch.  
  
I created a file called [example/standalone_bernoulli_tgamma.cpp](https://github.com/boostorg/multiprecision/blob/enhanced_gcc_clang_warnings/example/standalone_bernoulli_tgamma.cpp) that can use different MP types.  
- Calculate a vector of Bernoulli numbers (not using Math)  
- Use Stirling's Approximation to calculate some Gamma function values (not using Math).  
- Compare control values.  
- The file can either use/not-use `<boost/math/constants/constants.hpp>` with the flag `EXAMPLE008_BERNOULLI_USE_LOCAL_PI` located [here](https://github.com/boostorg/multiprecision/blob/4052c420ac319ae92edec69f656f697644550174/example/standalone_bernoulli_tgamma.cpp#L17).  
  
So when I compile locally with:  
  
```  
g++ -Wall -Wextra -Wconversion -Wsign-conversion -Wshadow -Wundef -O3 -std=c++11 -I/mnt/c/boost/modular_boost/boost/libs/multiprecision/include -I/mnt/c/boost/modular_boost/boost/libs/config/include standalone_bernoulli_tgamma.cpp -o standalone_bernoulli_tgamma.exe  
```  
  
I end up with a few tens of really sensible warnings. Notice that I'm not yet using `-Wpedantic`, as this clashes with `__float128`.  
  
We will need a bunch of other codes/tests and ways to find warnings, but this little file has given me an overall picture of what's going on in number, dec-float, bin-float and a few other places.  
  
Using enhanced GCC warnings definitely seems feasible. So Matt (@mborland) if you'd like to check that out, I'd be interested in what you think.  
  
Cc: @jzmaddock and @NAThompson

---

## Comment 6

> Username: ckormanyos  
> Created at: 2022-04-24 08:33:20 UTC  
> Updated at: 2022-04-24 08:33:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/445#issuecomment-1107790899  

A first step reducing high-level warns in `cpp_int.hpp`and `cpp_bin_float.hpp` and some of their included helper file(s) has been undertaken in #447.

---

## Comment 7

> Username: ckormanyos  
> Created at: 2022-05-01 08:25:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/445#issuecomment-1114170413  

So we have handled the first pass at warnings now in  
- `cpp_int`  
- `cpp_dec_float`  
- `cpp_bin_float`  
- `gmp_float`  
  
Along the way handled div-by zero found in some GMP program artifacts (as in #452, etc.).  
  
Up next is first pass warn handling in `mpfr_float`.  
  
Steps after that may include:  
- Finding warnings in the remaining parts of multiprecision.  
- Activate additional warnings such as missing `default` case in `switch`.  
- Find and remove (if now possible) some `#pragma` warning disable/enable pairs.  
- Big open point is still how to handle `__int128` and `__float128` in the presence of `-Wpedantic`.  
  
Cc: @jzmaddock and @mborland and @NAThompson
