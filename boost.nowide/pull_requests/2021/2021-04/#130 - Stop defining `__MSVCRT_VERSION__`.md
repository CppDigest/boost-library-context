# #130 Stop defining `__MSVCRT_VERSION__` [Merged]

> Username: lazka  
> Created at: 2021-04-05 15:23:58 UTC  
> Updated at: 2021-04-06 12:34:56 UTC  
> Merged at: 2021-04-05 18:30:17 UTC  
> Closed at: 2021-04-05 18:30:17 UTC  
> Url: https://github.com/boostorg/nowide/pull/130  

`__MSVCRT_VERSION__` is a mingw internal and shouldn't be set by the user.  
  
Setting it to something different than the toolchain provided value means the  
headers and crt wont match which can result in crashes and build errors.  
  
This fixes the boost build when targeting ucrt.  
  
Downstream issue for context: https://github.com/msys2/MINGW-packages/issues/8283

---

## Comment 1

> Username: lazka  
> Created_at: 2021-04-05 16:08:26 UTC  
> Url: https://github.com/boostorg/nowide/pull/130#issuecomment-813475603  

There some related discussion for a similar PR here: https://github.com/boostorg/log/pull/149

---

## Comment 2

> Username: Flamefire  
> Created_at: 2021-04-05 18:30:09 UTC  
> Url: https://github.com/boostorg/nowide/pull/130#issuecomment-813561317  

For reference this was introduced in https://github.com/boostorg/nowide/commit/d6ebda508f41713bd91620c69e47ff202cce9a6f  
  
I checked that with this PR the value of `__MSVCRT_VERSION__` on MinGW32 is still `0x0600` but the _wstat functions seem to be available nonetheless.  
I did some tests and the define doesn't seem to be required anymore and CI also passes, hence I'll accept this.  
  
Thanks a lot!

---

## Comment 3

> Username: lazka  
> Created_at: 2021-04-05 18:35:04 UTC  
> Updated_at: 2021-04-05 18:44:18 UTC  
> Url: https://github.com/boostorg/nowide/pull/130#issuecomment-813564150  

Thanks!  
  
I only noticed later that this was initially for mingw32, while I only tested with mingw-w64. Glad it works nevertheless :)

---

## Comment 4

> Username: Flamefire  
> Created_at: 2021-04-05 18:45:24 UTC  
> Url: https://github.com/boostorg/nowide/pull/130#issuecomment-813569877  

> I only noticed later that this was for mignw32  
  
Just for my understanding: What do you mean exactly by "this"?  
Am I still right assuming that this define lead to problems with mingw-w64?

---

## Comment 5

> Username: lazka  
> Created_at: 2021-04-05 18:57:57 UTC  
> Url: https://github.com/boostorg/nowide/pull/130#issuecomment-813576899  

with "this" I meant the define.  
  
Yes, this PR fixes mingw-w64 builds when not targeting old msvcrt, such as ucrt.

---

## Comment 6

> Username: glenfe  
> Created_at: 2021-04-06 12:34:56 UTC  
> Url: https://github.com/boostorg/nowide/pull/130#issuecomment-814084087  

OK to merge to master.

---
