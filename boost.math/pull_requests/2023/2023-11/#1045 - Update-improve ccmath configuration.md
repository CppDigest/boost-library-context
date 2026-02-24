# #1045 Update/improve ccmath configuration. [Merged]

> Username: jzmaddock  
> Created at: 2023-11-03 18:32:43 UTC  
> Updated at: 2024-01-24 21:33:40 UTC  
> Merged at: 2023-11-05 11:30:50 UTC  
> Closed at: 2023-11-05 11:30:50 UTC  
> Url: https://github.com/boostorg/math/pull/1045  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-11-04 16:18:47 UTC  
> Url: https://github.com/boostorg/math/pull/1045#issuecomment-1793487149  

@mborland most of the changes here are to ccmath: does this look OK to you?  The drone failures are due to github falling over yesterday BTW, I don't see an easy way to re-run that?

---

## Comment 2

> Username: mborland  
> Created_at: 2023-11-04 16:23:05 UTC  
> Url: https://github.com/boostorg/math/pull/1045#issuecomment-1793488086  

> @mborland most of the changes here are to ccmath: does this look OK to you? The drone failures are due to github falling over yesterday BTW, I don't see an easy way to re-run that?  
  
It looks good to me. I have organizational access to drone so I restarted the build. The status should be updated here on re-run.

---

## Comment 3

> Username: mborland  
> Created_at: 2023-11-04 16:24:35 UTC  
> Url: https://github.com/boostorg/math/pull/1045#issuecomment-1793488409  

By the way once you merge the PR to merge to master for next release (https://github.com/boostorg/math/pull/1042) will also automatically pull your commits in.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2023-11-05 11:30:19 UTC  
> Url: https://github.com/boostorg/math/pull/1045#issuecomment-1793710345  

Thanks Matt, one last failure is a network timeout, merging...

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2023-11-05 13:52:05 UTC  
> Url: https://github.com/boostorg/math/pull/1045#issuecomment-1793744048  

I don't know _why_ this was never a problem before, but nested namespaces are C++17 and I just had a MSVC compiler report this when set to C++14 at lines like [this](https://github.com/boostorg/math/blob/11e2348372088f0ede36ff6cbf2d70c8e1783a3b/include/boost/math/ccmath/isnan.hpp#L16)

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2023-11-05 13:54:22 UTC  
> Url: https://github.com/boostorg/math/pull/1045#issuecomment-1793744542  

Ah OK. It _should_ be caught by the preprocessor. Now how on earth did I set a MSVC compiler to C++14 and still reach that line of code? I will investigate...

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2023-11-05 17:33:33 UTC  
> Updated_at: 2023-11-05 17:35:17 UTC  
> Url: https://github.com/boostorg/math/pull/1045#issuecomment-1793798975  

OK. John and Matt. Sorry to report, ... but There seems to be some kind of very obscure problem. Sadly I must report it.  
@jzmaddock and @mborland   
  
- I take VS2019 and make a test project.  
- Set language standard to C++14  
- Then `#include <boost/math/ccmath/isinf.hpp>`  
- But I include `isinf.hpp` only _after_ including _number_ from multiprecision.  
  
And I get this:  
  
![grafik](https://github.com/boostorg/math/assets/2404721/cde2d55f-2d0a-4fa0-bb95-36206e263abe)

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2023-11-05 17:36:30 UTC  
> Url: https://github.com/boostorg/math/pull/1045#issuecomment-1793799593  

Oh well, you know, MSVC doesn't really adhere to the well-known `__cplusplus` values. So maybe something there?  
  
Anyway, here is the complete code:  
  
```cpp  
#include <boost/multiprecision/number.hpp>  
#include <boost/math/ccmath/isinf.hpp>  
  
auto main() -> int  
{  
}  
```

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2023-11-05 17:43:53 UTC  
> Url: https://github.com/boostorg/math/pull/1045#issuecomment-1793801296  

Sorry guys this is really garbled since it literally just crossed my path and I need to isolate better.  
  
This!!! is the offending code:  
  
```cpp  
#define BOOST_MP_STANDALONE  
#define BOOST_MATH_STANDALONE  
  
#include <boost/config.hpp>  
#include <boost/multiprecision/number.hpp>  
#include <boost/math/ccmath/isinf.hpp>  
  
auto main() -> int  
{  
}  
```  
  
![grafik](https://github.com/boostorg/math/assets/2404721/e5a3e908-1395-40ba-af40-0a14fd53bbe8)

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2023-11-05 18:55:01 UTC  
> Url: https://github.com/boostorg/math/pull/1045#issuecomment-1793816493  

It's the standalone bit that does this - it means we have no Boost.Config, so either we need to completely copy all of it's detection logic, or accept that the helpful #error message doesn't get triggered in this case, and rely on the compiler rejecting C++17 ism's which it does perfectly well IMO.  
  
I might be able to improve things a little for latest MSVC releases which have the C++20 feature detection macros, but not for older releases which do not.  Not sure it's completely worth it anyway, the existing error message is pretty clear that this needs C++17 to compile?

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2023-11-05 19:14:58 UTC  
> Url: https://github.com/boostorg/math/pull/1045#issuecomment-1793821006  

Ah... except that breaks client code that relies on BOOST_MATH_NO_CCMATH being set when the header isn't supported.... there may be an easy fix too, give me a moment!

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2023-11-05 19:33:22 UTC  
> Url: https://github.com/boostorg/math/pull/1045#issuecomment-1793825265  

Improvement in https://github.com/boostorg/math/pull/1046.

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2023-11-05 20:41:27 UTC  
> Url: https://github.com/boostorg/math/pull/1045#issuecomment-1793840063  

> Ah... except that breaks client code that relies on BOOST_MATH_NO_CCMATH being set when the header isn't supported.... there may be an easy fix too, give me a moment!  
  
Thank you John. I can confirm for my client code, it is fixed in your new PR #1046

---
