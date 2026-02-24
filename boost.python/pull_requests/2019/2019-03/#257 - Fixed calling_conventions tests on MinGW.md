# #257 Fixed calling_conventions tests on MinGW [Open]

> Username: Kojoley  
> Created at: 2019-03-27 19:16:22 UTC  
> Updated at: 2020-11-14 02:30:48 UTC  
> Url: https://github.com/boostorg/python/pull/257  

On MinGW calling conversion are actually macros that are expanded into a GCC attribute (e.g. `__fastcall` expands into `__attribute__((__fastcall__))`).

---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-04-02 17:04:03 UTC  
> Url: https://github.com/boostorg/python/pull/257#issuecomment-479096738  

Ping

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2019-04-02 17:43:43 UTC  
> Url: https://github.com/boostorg/python/pull/257#issuecomment-479114673  

Sorry for my silence. Me not being a Windows developer I feel a bit uncomfortable reviewing this. Do you think it would be possible to add a MinGW (cross-)compiler to the mix of compilers we use on Travis-CI or AppVeyor ? That would help prove that things work as intended.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-04-02 17:49:06 UTC  
> Url: https://github.com/boostorg/python/pull/257#issuecomment-479117231  

I seen other boost projects use mingw on appveyor, I could have added it here, but I do know nothing about faber (and it seems to not run module tests https://github.com/boostorg/python/issues/254).

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2019-04-02 17:52:17 UTC  
> Url: https://github.com/boostorg/python/pull/257#issuecomment-479118692  

OK, fair point. I'll thus try to add MinGW to the mix, then we should be able to see whether this PR fixes one (or more) failures.

---
