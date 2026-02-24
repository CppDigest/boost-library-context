# #152 Re-enable libstdc++ 4.8 [Closed]

> Username: pdimov  
> Created at: 2017-11-08 19:13:52 UTC  
> Updated at: 2018-08-05 04:52:33 UTC  
> Closed at: 2018-06-27 10:45:32 UTC  
> Url: https://github.com/boostorg/fiber/pull/152  

This re-enables libstdc++ 4.8 by removing the `cxx11_hdr_regex` requirement everywhere except on `topology_test`, replacing it with a check in `topology.cpp`.

---

## Comment 1

> Username: olk  
> Created_at: 2017-11-15 09:02:14 UTC  
> Url: https://github.com/boostorg/fiber/pull/152#issuecomment-344528478  

Hm - I'm not sure if I should re-enable gcc-4.8? Gcc-4.8 doesn't have a working regex impl (as you mentioned). Without regex, the NUMA support doesn't work.

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-11-15 12:51:19 UTC  
> Url: https://github.com/boostorg/fiber/pull/152#issuecomment-344583023  

Yes but everything else does.

---

## Comment 3

> Username: lethe555  
> Created_at: 2018-06-27 08:08:21 UTC  
> Url: https://github.com/boostorg/fiber/pull/152#issuecomment-400581599  

Will this be merged ?  for now we have to use old boost version 1.65

---

## Comment 4

> Username: olk  
> Created_at: 2018-06-27 09:30:43 UTC  
> Url: https://github.com/boostorg/fiber/pull/152#issuecomment-400606928  

could you verify that branch develop is working with libstdc++ 4.8, please

---

## Comment 5

> Username: lethe555  
> Created_at: 2018-06-27 10:37:41 UTC  
> Updated_at: 2018-06-27 10:42:46 UTC  
> Url: https://github.com/boostorg/fiber/pull/152#issuecomment-400626799  

I compiled  branch develop successfully , and all tests passed  
Edit: only tested static build , didn't test all build variant types

---

## Comment 6

> Username: olk  
> Created_at: 2018-06-27 10:45:32 UTC  
> Url: https://github.com/boostorg/fiber/pull/152#issuecomment-400629306  

ty, merged to master

---
