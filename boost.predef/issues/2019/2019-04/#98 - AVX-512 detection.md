# #98 - AVX-512 detection? [Open]

> Username: NAThompson  
> Created at: 2019-04-28 17:09:28 UTC  
> Updated at: 2020-03-01 11:30:19 UTC  
> Url: https://github.com/boostorg/predef/issues/98  

The file  
  
boost/predef/hardware/simd/x86.h  
  
has macros for detection of AVX and AVX-2 instructions, but not AVX-512.  
  
Could it be added? Or will it be too much trouble since AVX-512 is not a monolithic instruction set?

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-02-29 23:03:29 UTC  
> Url: https://github.com/boostorg/predef/issues/98#issuecomment-593003633  

It's possible, and there is the MIC detection which is AVX-512. Is MIC the only cpu which implements AVX-512? But yes, the problem would be that in the current arrangement it would not be possible to define the individual subsets. It would have to be a single version number to identify it. Perhaps it could be an alternate/alias to MIC? @jfalcou and @ccharly what do you think?

---

## Comment 2

> Username: jfalcou  
> Created at: 2020-03-01 11:30:19 UTC  
> Url: https://github.com/boostorg/predef/issues/98#issuecomment-593086543  

My stance on this matter as I tried to implement that in SPY :  
 - Have a AVX512 detection macro ghat basically means you have 512 bits simd register  
 - have a boolean macro for each sub system because avx512F does not mean Avx512dq is here  
  
Alternatively, we could have a version number for avx like 50000 and use bit field in the lower bits to mask tets each subsystem. This means maybe more macro
