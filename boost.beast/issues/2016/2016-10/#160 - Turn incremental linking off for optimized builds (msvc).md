# #160 - Turn incremental linking off for optimized builds (msvc) [Closed]

> Username: vinniefalco  
> Created at: 2016-10-28 22:30:36 UTC  
> Updated at: 2017-07-25 17:44:06 UTC  
> Closed at: 2016-11-04 15:18:57 UTC  
> Url: https://github.com/boostorg/beast/issues/160  

MSVC optimized builds have /LTCG (link time code generation) which causes a warning with /INCREMENTAL being turned on.  
  
A related CMake flag is `MSVC_INCREMENTAL_DEFAULT` but there are difficulties with changing that.
