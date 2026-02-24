# #40 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74 [Closed]

> Username: eldiener  
> Created at: 2020-04-19 18:25:23 UTC  
> Updated at: 2020-05-10 21:23:10 UTC  
> Closed at: 2020-05-06 17:47:56 UTC  
> Url: https://github.com/boostorg/boost_install/pull/40  



---

## Comment 1

> Username: eldiener  
> Created_at: 2020-05-06 12:23:42 UTC  
> Url: https://github.com/boostorg/boost_install/pull/40#issuecomment-624617560  

Can this please be merged ?

---

## Comment 2

> Username: pdimov  
> Created_at: 2020-05-06 12:51:01 UTC  
> Url: https://github.com/boostorg/boost_install/pull/40#issuecomment-624629858  

No, sorry. This is the verbatim source of zlib, used here purely for testing purposes. I don't see what business is it of ours.

---

## Comment 3

> Username: eldiener  
> Created_at: 2020-05-06 13:03:24 UTC  
> Url: https://github.com/boostorg/boost_install/pull/40#issuecomment-624635919  

The business of ours is that it is incorrect when referring to Embarcadero C++ clang-based compilers. Are you really going to argue that we can not change it because it is "verbatim source" so that a compiler can be used correctly with boost_install ?

---

## Comment 4

> Username: pdimov  
> Created_at: 2020-05-06 13:16:50 UTC  
> Url: https://github.com/boostorg/boost_install/pull/40#issuecomment-624642909  

boost_install is a b2 module, so patching zlib has no significance to whether it can be used with a particular compiler or not. What problem are you fixing here?

---

## Comment 5

> Username: eldiener  
> Created_at: 2020-05-06 15:35:09 UTC  
> Url: https://github.com/boostorg/boost_install/pull/40#issuecomment-624722178  

>   
>   
> boost_install is a b2 module, so patching zlib has no significance to whether it can be used with a particular compiler or not. What problem are you fixing here?  
  
Is not the zlib source part of boost_install ? if not why is it part of the source files ?

---

## Comment 6

> Username: pdimov  
> Created_at: 2020-05-06 15:41:17 UTC  
> Url: https://github.com/boostorg/boost_install/pull/40#issuecomment-624725680  

It's part of the test suite. The tests of boost_install build and install Boost libraries and check whether they are usable from CMake. The zlib source is used to build Iostreams with deflate support on Windows/Appveyor.  
  
Since no Borland compiler is being tested on Appveyor, it doesn't matter whether zlib would compile under it.

---

## Comment 7

> Username: eldiener  
> Created_at: 2020-05-06 17:37:08 UTC  
> Url: https://github.com/boostorg/boost_install/pull/40#issuecomment-624788795  

>   
>   
> It's part of the test suite. The tests of boost_install build and install Boost libraries and check whether they are usable from CMake. The zlib source is used to build Iostreams with deflate support on Windows/Appveyor.  
>   
> Since no Borland compiler is being tested on Appveyor, it doesn't matter whether zlib would compile under it.  
  
But obviously it will matter if we can get Embarcadero C++ clang-based tests running in some sort of CI environment or if we can do regresssion testing for the compiler. I am working on a PR for zlib. But you can close this PR if you wish.

---

## Comment 8

> Username: eldiener  
> Created_at: 2020-05-10 21:23:10 UTC  
> Url: https://github.com/boostorg/boost_install/pull/40#issuecomment-626391817  

I submitted a PR to zlib for adding Embarcadero C++ clang-based support ( https://github.com/madler/zlib/pull/490 ). The open PRs for zlib are currently at 142 going back to 2013. This is why I think that sometimes Boost needs to fix things for themselves rather than wait for some 3rd party library to do so.

---
