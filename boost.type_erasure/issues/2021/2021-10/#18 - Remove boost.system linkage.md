# #18 - Remove boost.system linkage [Closed]

> Username: hdu-sdlzx  
> Created at: 2021-10-05 09:00:25 UTC  
> Updated at: 2021-11-26 01:51:32 UTC  
> Closed at: 2021-11-26 01:51:32 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/18  

Boost.system is header-only now.  
It was required by boost.thread so there is an explicit linkage in Jamfile.v2.
