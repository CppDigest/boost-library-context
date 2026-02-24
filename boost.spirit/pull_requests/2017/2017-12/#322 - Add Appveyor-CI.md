# #322 Add Appveyor-CI [Merged]

> Username: Kojoley  
> Created at: 2017-12-12 20:24:30 UTC  
> Updated at: 2017-12-13 21:11:52 UTC  
> Merged at: 2017-12-13 21:11:48 UTC  
> Closed at: 2017-12-13 21:11:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/322  

Mostly the same as #291.  
  
There is no `ccache` on Windows, so I am using `clcache`.  
VS2017 build with `/std:c++latest` might be added later.  
  
The build times are disaster.

---
