# #77 - linux_error.hpp deprecation [Closed]

> Username: maximilianriemensberger  
> Created at: 2021-12-22 12:50:31 UTC  
> Updated at: 2021-12-27 00:53:31 UTC  
> Closed at: 2021-12-27 00:53:12 UTC  
> Url: https://github.com/boostorg/system/issues/77  

When updating to boost 1.78 from an older version, inoticed that commit https://github.com/boostorg/system/commit/8f32183b63b478a1b3474f4c01e931d2821a5787 introduced deprecation warnings for the platform specific error code.  I'm using linux_error.hpp to detect non-fatal errors when accepting TCP sockets.  There are a few of those defined in linux_error.hpp  
  
Is it be possible to un-deprecate the header (similarly to https://github.com/boostorg/system/issues/57)?

---

## Comment 1

> Username: pdimov  
> Created at: 2021-12-27 00:53:31 UTC  
> Url: https://github.com/boostorg/system/issues/77#issuecomment-1001269218  

Yes of course.
