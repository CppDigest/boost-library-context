# #232 - LNK2005 error of boost::archive::codecvt_null<wchar_t> [Open]

> Username: Mitsuhiko-Matsukawa  
> Created at: 2021-04-19 10:07:02 UTC  
> Updated at: 2023-01-19 08:33:46 UTC  
> Url: https://github.com/boostorg/serialization/issues/232  

When I use boost/archive/xml_wiarchive.hpp in dll and exe together, LNK2005 error occurs.  
boost 1_76_0, Visual Studio 2019  
  
[LNK2005_codecvt_null.zip](https://github.com/boostorg/serialization/files/6334947/LNK2005_codecvt_null.zip)  
  
Please download and put as below.  
boost_1_76_0  
-boost  
-stage  
--x64  
--x86  
LNK2005_codecvt_null  
-LNK2005_codecvt_null.sln

---

## Comment 1

> Username: Mitsuhiko-Matsukawa  
> Created at: 2021-07-25 03:29:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/232#issuecomment-886141459  

@robertramey   
If I revert these files before Jun 19, 2020, my test code works.  
* serialization/include/boost/archive/codecvt_null.hpp  
* serialization/src/codecvt_null.cpp

---

## Comment 2

> Username: Mitsuhiko-Matsukawa  
> Created at: 2021-07-27 15:15:37 UTC  
> Url: https://github.com/boostorg/serialization/issues/232#issuecomment-887599624  

@robertramey  
If I revert commit 812ef99dea96a5b74de58df4c8437821908e5ae4 from master branch, my test code works.

---

## Comment 3

> Username: AdmiralPellaeon  
> Created at: 2022-02-15 14:45:22 UTC  
> Updated at: 2022-02-15 14:45:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/232#issuecomment-1040357217  

@Mitsuhiko-Matsukawa I have the same issues. I tried your workaround for boost 1.78.0, but this leads to a different linker error. Did you already test it with the newest boost version?  
Atm I try to use boost with shared linking to overcome with this problem. But I would prefer to link it statically how I did it all the time before.

---

## Comment 4

> Username: robertramey  
> Created at: 2022-02-24 22:43:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/232#issuecomment-1050334292  

what is the current status on this? I've tested extensively with both static and shared linking with no problems.

---

## Comment 5

> Username: Mitsuhiko-Matsukawa  
> Created at: 2022-08-24 11:08:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/232#issuecomment-1225573434  

@robertramey @AdmiralPellaeon   
This issue still exists on boost1.80.0 + Visual Studio 2019.  
  
I have applied this patch and the problem has been resolved.  
https://github.com/boostorg/serialization/pull/240  
  
The idea of this patch is simple.  
Move implementations from cpp to hpp so that they will be inline and linker error doesn't occur.  
  
@robertramey   
Why did you move them from hpp to cpp? Is there a good point to do that?

---

## Comment 6

> Username: robertramey  
> Created at: 2022-09-15 21:17:01 UTC  
> Url: https://github.com/boostorg/serialization/issues/232#issuecomment-1248640947  

I'm still trying to understand what's going on here.  When you "move" this implementation, do you rebuild the libraries when you verify that "it works".

---

## Comment 7

> Username: Mitsuhiko-Matsukawa  
> Created at: 2022-12-20 07:39:18 UTC  
> Url: https://github.com/boostorg/serialization/issues/232#issuecomment-1358952291  

@robertramey   
Have you downloaded [this file](https://github.com/boostorg/serialization/files/6334947/LNK2005_codecvt_null.zip) and tried to build?  
  
> When you "move" this implementation, do you rebuild the libraries when you verify that "it works".  
  
Yes, I have rebuilt the libraries and succeeded to link to my test code.  
My test code fails to link before I move but succeeds after I move.  
I have tested only for Visual Studio 2019. It might be error for other platforms. In that case, we should switch by preprocessor.  
This issue still exists on 1.81.0.

---

## Comment 8

> Username: oicchris  
> Created at: 2023-01-19 08:33:46 UTC  
> Url: https://github.com/boostorg/serialization/issues/232#issuecomment-1396613166  

Hi!  I ran into the same link error and saw this open issue.  I just wanted to comment, adding the `boost_serialization-vc140-mt.lib` to my build fixed the link error.  Library was provided by vcpkg, boost v1.81.0 shared library.  I hope that solves your issue as well!
