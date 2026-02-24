# #33 Fix old GCC warning about attributes in declarations [Merged]

> Username: igaztanaga  
> Created at: 2018-09-08 23:40:11 UTC  
> Updated at: 2018-09-09 20:44:44 UTC  
> Merged at: 2018-09-09 20:44:05 UTC  
> Closed at: 2018-09-09 20:44:05 UTC  
> Url: https://github.com/boostorg/chrono/pull/33  

"warning: type attributes are honored only at type definition"

---

## Comment 1

> Username: viboes  
> Created_at: 2018-09-09 11:36:25 UTC  
> Url: https://github.com/boostorg/chrono/pull/33#issuecomment-419709749  

Hi Ion,   
  
I don't know if this has a sense for some versions of Windows. Are you sure that we don't need them in any versions?

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2018-09-09 19:05:23 UTC  
> Url: https://github.com/boostorg/chrono/pull/33#issuecomment-419737398  

I tested the change in several MSVC compilers: I haven't found any problem with msvc-7.1, 8.0, 9.0, 10.0, 11.0, 12.0, 14.0 & 14.1.

---

## Comment 3

> Username: viboes  
> Created_at: 2018-09-09 20:44:44 UTC  
> Url: https://github.com/boostorg/chrono/pull/33#issuecomment-419743730  

Thanks

---
