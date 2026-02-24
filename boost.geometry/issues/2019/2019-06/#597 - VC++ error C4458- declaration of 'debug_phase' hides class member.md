# #597 - VC++ error C4458: declaration of 'debug_phase' hides class member [Closed]

> Username: vschoech  
> Created at: 2019-06-21 08:54:05 UTC  
> Updated at: 2020-03-09 10:14:42 UTC  
> Closed at: 2019-06-28 08:37:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/597  

I am using Microsoft Visual Studio 15.8.0 (VCToolsVersion 14.15.26726) to compile boost 1.70.0. I use compiler flag /W4. The following error occurs in geometry:  
  
> \boost_1_70_0\boost\geometry\algorithms\detail\is_valid\multipolygon.hpp(284): error C4458: declaration of 'debug_phase' hides class member  
  
See also:  
https://svn.boost.org/trac10/ticket/13377

---

## Comment 1

> Username: barendgehrels  
> Created at: 2019-06-26 09:19:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/597#issuecomment-505794037  

Thanks for the report Volker, fixed in 1.71

---

## Comment 2

> Username: barendgehrels  
> Created at: 2019-06-28 08:37:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/597#issuecomment-506653486  

Also this issue is now fixed and merged, will close it. Thanks for the heads up and your patience.

---

## Comment 3

> Username: vschoech  
> Created at: 2020-03-09 10:14:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/597#issuecomment-596441178  

I can confirm that as of boost 1.72.0 this is no longer reproducible in our code.
