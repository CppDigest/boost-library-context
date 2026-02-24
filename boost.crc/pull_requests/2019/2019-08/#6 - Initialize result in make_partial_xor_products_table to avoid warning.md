# #6 Initialize result in make_partial_xor_products_table to avoid warning [Merged]

> Username: achartier  
> Created at: 2019-08-23 21:56:44 UTC  
> Updated at: 2023-09-27 17:33:13 UTC  
> Merged at: 2023-09-27 17:33:13 UTC  
> Closed at: 2023-09-27 17:33:13 UTC  
> Url: https://github.com/boostorg/crc/pull/6  

This fixes the following warning when compiling with MSVC:  
  
> crc.hpp(984) : warning C4701: potentially uninitialized local variable 'result' used

---

## Comment 1

> Username: achartier  
> Created_at: 2019-08-23 22:46:46 UTC  
> Url: https://github.com/boostorg/crc/pull/6#issuecomment-524484773  

Looks like the Travis build failures are just due to some package misconfiguration that is independent from this commit.

---

## Comment 2

> Username: FreddyFunk  
> Created_at: 2022-02-04 15:43:20 UTC  
> Url: https://github.com/boostorg/crc/pull/6#issuecomment-1030106109  

@achartier could you please retrigger the build? The error seems unrelated to the code..  
  
![image](https://user-images.githubusercontent.com/27208977/152558349-1277a22e-d4ad-4483-b80a-07e19989c0e4.png)

---

## Comment 3

> Username: achartier  
> Created_at: 2022-02-05 00:13:37 UTC  
> Url: https://github.com/boostorg/crc/pull/6#issuecomment-1030452317  

I tried to trigger a new build by closing/reopening the request, but the job was rejected: https://travis-ci.org/github/boostorg/crc/requests  
  
I'm not familiar with this Jarvis interface, is there a better way to trigger this build? Other than closing this request and creating a new one?

---
