# #600 - VC++ warning C4456: declaration of 'prev' hides previous local declaration [Closed]

> Username: vschoech  
> Created at: 2019-06-21 09:05:07 UTC  
> Updated at: 2020-03-09 10:14:05 UTC  
> Closed at: 2019-06-28 08:36:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/600  

I am using Microsoft Visual Studio 15.8.0 (VCToolsVersion 14.15.26726) to compile boost 1.70.0. I use compiler flag /W4. The following warning occurs in geometry:  
  
> \boost_1_70_0\boost\geometry\algorithms\detail\is_valid\has_spikes.hpp(149): warning C4456: declaration of 'prev' hides previous local declaration  
  
See also:  
https://svn.boost.org/trac10/ticket/13598

---

## Comment 1

> Username: barendgehrels  
> Created at: 2019-06-26 09:06:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/600#issuecomment-505789501  

Thanks Volker, this one is fixed now, sorry for the delay. Target is 1.71

---

## Comment 2

> Username: barendgehrels  
> Created at: 2019-06-28 08:36:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/600#issuecomment-506653308  

It is fixed and merged, will close it. Thanks for the heads up and your patience.

---

## Comment 3

> Username: vschoech  
> Created at: 2020-03-09 10:14:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/600#issuecomment-596440918  

I can confirm that as of boost 1.72.0 this is no longer reproducible in our code.
