# #598 - VC++ error C4244: 'argument': conversion from 'const coordinate_type' to 'const int', possible loss of data [Closed]

> Username: vschoech  
> Created at: 2019-06-21 09:00:04 UTC  
> Updated at: 2020-03-09 10:12:42 UTC  
> Closed at: 2019-06-26 10:08:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/598  

I am using Microsoft Visual Studio 15.8.0 (VCToolsVersion 14.15.26726) to compile boost 1.70.0. I use compiler flag /W4. The following error occurs in geometry:  
  
> \boost_1_70_0\boost\geometry\strategies\cartesian\expand_point.hpp(66): error C4244: 'argument': conversion from 'const coordinate_type' to 'const int', possible loss of data  
  
See also:  
https://svn.boost.org/trac10/ticket/13597

---

## Comment 1

> Username: barendgehrels  
> Created at: 2019-06-26 10:08:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/598#issuecomment-505810941  

This issue is nearly duplicate with #601  - the only difference is the compiler? Do you mind if I close this one as duplicate?

---

## Comment 2

> Username: vschoech  
> Created at: 2020-03-09 10:12:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/598#issuecomment-596440326  

I can confirm that as of boost 1.72.0 this is no longer an issue in our code.
