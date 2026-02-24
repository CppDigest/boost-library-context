# #126 - std::bind2nd is removed in c++17 [Closed]

> Username: gmoryes  
> Created at: 2018-08-16 18:47:11 UTC  
> Updated at: 2022-05-20 09:15:10 UTC  
> Closed at: 2018-08-27 18:20:41 UTC  
> Url: https://github.com/boostorg/gil/issues/126  

It used here:  
  
1) `include/boost/gil/planar_pixel_iterator.hpp`  
2) `include/boost/gil/extension/numeric/pixel_numeric_operations.hpp`  
It was deprecated in c++17. Could you use `std::bind` + `std::placeholders` instead std::bind2nd?  
  
Thanks.

---

## Comment 1

> Username: mloskot  
> Created at: 2018-08-16 18:50:06 UTC  
> Url: https://github.com/boostorg/gil/issues/126#issuecomment-413647900  

Yes, we are aware.  
  
However, GIL has not switched to C++17 yet. Currently, GIL requires C++11 compiler.

---

## Comment 2

> Username: gmoryes  
> Created at: 2018-08-17 10:04:08 UTC  
> Url: https://github.com/boostorg/gil/issues/126#issuecomment-413819484  

Okey, but the fix consists of 3 line. You still will not do it?  
If no - what about pull request?

---

## Comment 3

> Username: mloskot  
> Created at: 2018-08-17 10:22:51 UTC  
> Updated at: 2018-08-17 10:23:29 UTC  
> Url: https://github.com/boostorg/gil/issues/126#issuecomment-413823617  

Yes, I know and yes we will do it when we will be switching over to C++17 or when one of us finds time to make such refactoring.  
  
If you wish to see this change in GIL earlier, then please submit pull request. (We would not have this unnecessary discussion if you just sent the PR straight away :-))

---

## Comment 4

> Username: gmoryes  
> Created at: 2018-08-17 10:39:15 UTC  
> Url: https://github.com/boostorg/gil/issues/126#issuecomment-413827025  

Okey. I see :)

---

## Comment 5

> Username: gmoryes  
> Created at: 2018-08-17 11:22:04 UTC  
> Url: https://github.com/boostorg/gil/issues/126#issuecomment-413835665  

https://github.com/boostorg/gil/pull/127

---

## Comment 6

> Username: mloskot  
> Created at: 2022-05-20 09:15:09 UTC  
> Url: https://github.com/boostorg/gil/issues/126#issuecomment-1132671189  

This is included in the planning towards C++14/17 discussion here https://github.com/boostorg/gil/discussions/676
