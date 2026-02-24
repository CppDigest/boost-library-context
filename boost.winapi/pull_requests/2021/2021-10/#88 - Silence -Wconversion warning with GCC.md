# #88 Silence -Wconversion warning with GCC [Closed]

> Username: igaztanaga  
> Created at: 2021-10-03 15:53:53 UTC  
> Updated at: 2021-10-03 19:24:08 UTC  
> Closed at: 2021-10-03 19:23:04 UTC  
> Url: https://github.com/boostorg/winapi/pull/88  

Some users add -Wconversion flag to detect integer promotion bugs while using Boost.Interprocess, which uses Boost.Winapi. This is a simple patch that make Boost.Interprocess -Wconversion happy, just explicitly casting the result of MAKELANGID, which internally uses a promotion to int while dealing with shorts.

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-10-03 19:24:08 UTC  
> Url: https://github.com/boostorg/winapi/pull/88#issuecomment-933010707  

Thanks, I've committed a slightly modified version that also cleans up our custom definition of `MAKELANGID_`.

---
