# #827 short-circuit overflow protection for M1 Mac clang [Merged]

> Username: mckib2  
> Created at: 2022-10-02 21:53:14 UTC  
> Updated at: 2022-10-14 02:28:16 UTC  
> Merged at: 2022-10-12 22:03:13 UTC  
> Closed at: 2022-10-12 22:03:13 UTC  
> Url: https://github.com/boostorg/math/pull/827  

Closes https://github.com/boostorg/math/issues/826  
xref https://github.com/scipy/scipy/issues/14901   
  
Splits single if statement into two to avoid apparent clang short-circuit bug on M1 Mac platforms.

---

## Comment 1

> Username: mborland  
> Created_at: 2022-10-11 16:41:23 UTC  
> Url: https://github.com/boostorg/math/pull/827#issuecomment-1274981957  

@mckib2 I added the test from your linked issue and verified the patch works on my M1 machine. Will merge once the CI cycles.

---
