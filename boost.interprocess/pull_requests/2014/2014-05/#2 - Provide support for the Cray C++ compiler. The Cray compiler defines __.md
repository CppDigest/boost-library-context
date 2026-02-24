# #2 Provide support for the Cray C++ compiler.  The Cray compiler defines __... [Merged]

> Username: rsdale  
> Created at: 2014-05-20 19:55:37 UTC  
> Updated at: 2014-06-20 07:37:28 UTC  
> Merged at: 2014-05-21 12:40:48 UTC  
> Closed at: 2014-05-21 12:40:48 UTC  
> Url: https://github.com/boostorg/interprocess/pull/2  

The Cray C++ compiler defines **GNUC** and generally supports gnu extensions to C and C++.   
It does not, however, support inline assembly.  The changes here are to avoid inline assembly when  
_CRAYC is asserted in the preprocessor.

---

## Comment 1

> Username: rsdale  
> Created_at: 2014-05-21 12:56:01 UTC  
> Url: https://github.com/boostorg/interprocess/pull/2#issuecomment-43750219  

Thanks!!  
  
##   
  
rsd  
On May 21, 2014, at 7:40 AM, Ion Gaztañaga notifications@github.com wrote:  
  
> Merged #2.  
>   
> —  
> Reply to this email directly or view it on GitHub.

---
