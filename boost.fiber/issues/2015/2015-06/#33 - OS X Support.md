# #33 - OS X Support [Closed]

> Username: git-patrick  
> Created at: 2015-06-01 18:51:14 UTC  
> Updated at: 2017-02-14 10:12:07 UTC  
> Closed at: 2015-09-27 18:30:34 UTC  
> Url: https://github.com/boostorg/fiber/issues/33  

I'm trying to get boost fiber to work on OS 10.10.4.  The first issue I had was the OS X version of Clang doesn't support thread_local for some reason.  I was considering switching fiber over to boost thread local storage instead of using thread_local, but I figured I would just try just using GCC-5.1.0 and link with stdlibc++ instead.  
  
Building boost modular and boost fiber was successful, and building the fiber example "simple.cpp" was also successful, but it fails with segmentation faults.  Any idea what's going on?  
  
Maybe I'll hack it to use boost thread local storage so the standard clang will work instead..

---

## Comment 1

> Username: olk  
> Created at: 2015-09-27 18:30:34 UTC  
> Url: https://github.com/boostorg/fiber/issues/33#issuecomment-143582865  

until Apple adds support for thread_local it will not work - sorry

---

## Comment 2

> Username: Rydgel  
> Created at: 2017-02-13 11:19:22 UTC  
> Url: https://github.com/boostorg/fiber/issues/33#issuecomment-279359420  

@olk Apple added support for thread_local since last Xcode.  
It now compiles fine, but gives some segfaults deep inside boost context.  
  
I can provide more informations if needed.

---

## Comment 3

> Username: olk  
> Created at: 2017-02-13 14:12:39 UTC  
> Url: https://github.com/boostorg/fiber/issues/33#issuecomment-279402723  

@Rydgel yes, but report in boost.context please

---

## Comment 4

> Username: Rydgel  
> Created at: 2017-02-14 08:54:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/33#issuecomment-279645362  

This is actually this bug https://github.com/boostorg/context/issues/38  
So I guess I'll just wait the next Boost release.

---

## Comment 5

> Username: olk  
> Created at: 2017-02-14 09:54:30 UTC  
> Url: https://github.com/boostorg/fiber/issues/33#issuecomment-279660837  

@Rydgel could you verify that the code is working, please - I don't own a Mac, so I can't test it

---

## Comment 6

> Username: Rydgel  
> Created at: 2017-02-14 10:06:43 UTC  
> Url: https://github.com/boostorg/fiber/issues/33#issuecomment-279663558  

@olk just recompiled Boost with the patch from that context issue. Everything is working now!

---

## Comment 7

> Username: olk  
> Created at: 2017-02-14 10:12:07 UTC  
> Url: https://github.com/boostorg/fiber/issues/33#issuecomment-279664784  

@Rydgel ty
