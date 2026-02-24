# #29 Added CI framework [Closed]

> Username: jeking3  
> Created at: 2018-08-25 14:16:04 UTC  
> Updated at: 2018-09-02 15:23:48 UTC  
> Closed at: 2018-08-25 14:16:27 UTC  
> Url: https://github.com/boostorg/locale/pull/29  

- travis with valgrind, cppcheck, ubsan, codecov, covscan (future)  
  - appveyor with MSVC 2010 through 2017, cygwin 32/64, mingw 32/64  
  - README, LICENSE, etc.

---

## Comment 1

> Username: artyom-beilis  
> Created_at: 2018-08-27 09:34:41 UTC  
> Url: https://github.com/boostorg/locale/pull/29#issuecomment-416171503  

Why can you closed it?

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-08-29 01:43:31 UTC  
> Url: https://github.com/boostorg/locale/pull/29#issuecomment-416795102  

It was not ready, I meant to submit it against my own fork, but I was on a mobile tether and the screen didn't update fast enough.

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-09-02 15:23:48 UTC  
> Url: https://github.com/boostorg/locale/pull/29#issuecomment-417938340  

If you want to look at a couple issues identified by valgrind and ubsan, check out https://travis-ci.org/jeking3/locale/builds/423599280.  I upgraded valgrind to the development tip and it got rid of some of the known sse_4_1 read overruns, but there's one left in test_formatting that needs to be looked at, and UBSAN is claiming a 160 byte read for a bool, or something.

---
