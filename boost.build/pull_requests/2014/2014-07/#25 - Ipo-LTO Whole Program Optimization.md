# #25 Ipo/LTO Whole Program Optimization [Closed]

> Username: bernhard-b  
> Created at: 2014-07-28 14:04:23 UTC  
> Updated at: 2021-10-02 21:18:55 UTC  
> Closed at: 2019-05-06 15:32:34 UTC  
> Url: https://github.com/boostorg/build/pull/25  

Added whole-program-optimization flags for msvc, intel-win and gcc. Splitted in two features to allow compilation of some source files without whole-program-optimization.

---

## Comment 1

> Username: vprus  
> Created_at: 2014-07-29 12:27:04 UTC  
> Url: https://github.com/boostorg/build/pull/25#issuecomment-50469206  

Hi,  
  
I have a couple questions:  
- Maybe we shall name the feature 'lto'? It's probably common terminology, and the fully spelling is kinda long.  
- Why do we need two features? If we want to compile certain files without lto, it's always possible to do::  
    
  obj a : a.cpp : <lto>off ;   
  
?

---

## Comment 2

> Username: bernhard-b  
> Created_at: 2014-08-02 15:20:48 UTC  
> Updated_at: 2014-08-14 12:44:38 UTC  
> Url: https://github.com/boostorg/build/pull/25#issuecomment-50965520  

i can rename it.  
One feature is for compiling the source the other is for the linker. A use case for this is:  
  
```  
lib a : a.cpp : <whole-program-optimization>on <link-time-optimization>on   
        : : <link-time-optimization>on ;  
lib b : b.cpp : <library>a ;  
```  
  
for example under msvc lib b must pass /LTCG (link-time-optimization on) to the linker as otherwise you will get a warning and the linker restarts itself with this flag. i don't know if this would be possible with only one feature?  
Another thing i don't know how to implement is that the intel compiler allows a third option for whole-program-optimization (single file optimization) as the normal ipo flag may run out of memory at least for 32-bit compilations. Is it possible to extend the allowed parameters of a feature?

---

## Comment 3

> Username: vprus  
> Created_at: 2014-08-14 12:50:46 UTC  
> Url: https://github.com/boostorg/build/pull/25#issuecomment-52178751  

It seems that using `<link-time-optimization>` in usage requirements is somewhat inconvenient - and in fact, behaviour of non-free features in usage requirements is not well defined. I think the goal here is that if any of the object files involved in a link was produced with /GL, we need to do link with /LTGC? It might be possible to modify linking generator for MSVC, so that it examines all input sources and sees whether they have <lto>on in properties, and use /LTGC in that case? Would that be a perfect user interface, in your opinion?

---

## Comment 4

> Username: bernhard-b  
> Created_at: 2014-08-25 18:02:07 UTC  
> Url: https://github.com/boostorg/build/pull/25#issuecomment-53301976  

yes examining the input sources will probably be better. As all compiler should benefit from it. Wouldn't it better to modify the run method of linking-generator?

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2019-05-06 15:32:34 UTC  
> Url: https://github.com/boostorg/build/pull/25#issuecomment-489664692  

Would be really nice to get some LTO support in. But since there hasn't been movement on this in a long time going to close it. And hope that a new PR comes in :-)

---
