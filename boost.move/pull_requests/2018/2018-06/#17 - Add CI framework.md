# #17 Add CI framework [Merged]

> Username: jeking3  
> Created at: 2018-06-19 06:27:14 UTC  
> Updated at: 2018-06-26 19:53:18 UTC  
> Merged at: 2018-06-19 15:57:45 UTC  
> Closed at: 2018-06-19 15:57:45 UTC  
> Url: https://github.com/boostorg/move/pull/17  

- travis with valgrind, cppcheck, ubsan, codecov, covscan (future)  
  - appveyor with MSVC 2010 through 2017, cygwin 32/64, mingw 32/64  
  - README, LICENSE, etc.

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-06-19 13:38:35 UTC  
> Url: https://github.com/boostorg/move/pull/17#issuecomment-398401842  

The UBSAN failure looks real but it is in an example... we should still fix it.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2018-06-19 16:00:02 UTC  
> Url: https://github.com/boostorg/move/pull/17#issuecomment-398452555  

Thanks for pull request

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-06-20 04:08:15 UTC  
> Url: https://github.com/boostorg/move/pull/17#issuecomment-398618147  

Sure, are you going to deal with the UBSAN failure separately?

---

## Comment 4

> Username: igaztanaga  
> Created_at: 2018-06-26 19:53:18 UTC  
> Url: https://github.com/boostorg/move/pull/17#issuecomment-400440937  

Looks that the latest changes have fixed the UBSAN report. Many thanks!

---
