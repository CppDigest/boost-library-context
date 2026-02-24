# #599 - VC++ warning C4127: conditional expression is constant [Closed]

> Username: vschoech  
> Created at: 2019-06-21 09:01:09 UTC  
> Updated at: 2020-03-09 10:10:52 UTC  
> Closed at: 2019-06-28 08:37:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/599  

I am using Microsoft Visual Studio 15.8.0 (VCToolsVersion 14.15.26726) to compile boost 1.70.0. I use compiler flag /W4. The following warning occurs in geometry:  
  
> \boost_1_70_0\boost\geometry\algorithms\detail\overlay\sort_by_side.hpp(372): warning C4127: conditional expression is constant  
  
See also:  
https://svn.boost.org/trac10/ticket/13596

---

## Comment 1

> Username: barendgehrels  
> Created at: 2019-06-26 09:07:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/599#issuecomment-505789809  

Thanks Volker, this one is also fixed, sorry for the delay. Target is 1.71

---

## Comment 2

> Username: barendgehrels  
> Created at: 2019-06-28 08:37:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/599#issuecomment-506653406  

This one is also fixed and merged, will close it. Thanks for the heads up and your patience.

---

## Comment 3

> Username: vschoech  
> Created at: 2020-03-09 10:10:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/599#issuecomment-596439542  

I can confirm that this issue is not reproducible in boost 1.72.0.
