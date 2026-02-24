# #29 update to 2014 CODATA recommended values [Open]

> Username: e-kwsm  
> Created at: 2018-07-23 07:39:54 UTC  
> Updated at: 2021-06-10 02:34:20 UTC  
> Url: https://github.com/boostorg/units/pull/29  

2006 CODATA recommended values are used.  
  
Significant changes:  
  
- https://github.com/boostorg/units/blob/d67f8e7a50ee013d2e71abde1d1eb058f0036812/include/boost/units/systems/si/codata/electron_constants.hpp#L75-L76  
  became 10x bigger (this should have been −183**8**.281970292)  
- https://github.com/boostorg/units/blob/d67f8e7a50ee013d2e71abde1d1eb058f0036812/include/boost/units/systems/si/codata/triton_constants.hpp#L63-L68  
  removed (since CODATA 2010); users can obtain by themselves

---

## Comment 1

> Username: jhunold  
> Created_at: 2018-09-14 08:27:54 UTC  
> Url: https://github.com/boostorg/units/pull/29#issuecomment-421271879  

The update of `mu_e_over_mu_N`is necessary. Simply removing the other three constants is not an option. We have to deprecate them first.

---

## Comment 2

> Username: e-kwsm  
> Created_at: 2018-09-18 04:15:54 UTC  
> Url: https://github.com/boostorg/units/pull/29#issuecomment-422249838  

> Simply removing the other three constants is not an option. We have to deprecate them first.  
  
I updated the values.  
  
BTW what is the standard way to deprecate a variable in boost? Other libraries use (wrappers of):  
  
- `[[deprecated]]` attribute (C++14): Boost.Atomic, Boost.Unordered  
- `__attribute__((deprecated))` (GCC and Clang extension): Boost.Atomic, Boost.Unordered  
- `__declspec(deprecated)` (MSVC extension): Boost.Asio, Boost.Atomic, Boost.Unordered  
- `#pragma message`: Boost.Accumulators  
- `#warning`: Boost.Accumulators

---

## Comment 3

> Username: jhunold  
> Created_at: 2018-09-19 17:55:02 UTC  
> Url: https://github.com/boostorg/units/pull/29#issuecomment-422898843  

We cannot use `[[deprecated]]`right now because this breaks C++03 compatibility. Which I won't do now.  
The last two are only useful (and currently used) for deprecation of whole headers. So a macro abstracting the compiler extensions would be nice. Thinking about it.

---

## Comment 4

> Username: e-kwsm  
> Created_at: 2019-06-06 10:10:34 UTC  
> Url: https://github.com/boostorg/units/pull/29#issuecomment-499434516  

[2018 CODATA recommended values](https://physics.nist.gov/cuu/Constants/Table/allascii.txt) are released. I will close this when I create a new PR for 2018.

---
