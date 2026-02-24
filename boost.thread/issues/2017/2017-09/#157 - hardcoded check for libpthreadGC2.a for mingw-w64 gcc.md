# #157 - hardcoded check for libpthreadGC2.a for mingw-w64 gcc [Closed]

> Username: hanetzer  
> Created at: 2017-09-21 08:43:17 UTC  
> Updated at: 2017-09-23 05:54:25 UTC  
> Closed at: 2017-09-23 05:54:25 UTC  
> Url: https://github.com/boostorg/thread/issues/157  

Since I can't reopen #156 and @viboes closed it without even looking into it I have to make a new one.  
  
The problem here is that boost has a hardcoded check for libpthreadGC2.a, when mingw-w64's runtime provides a posix thread library in the same naming and usage scheme, requiring no extra work on that part. You guys should take advantage of it, so end users won't have to install another otherwise unneeded dependency when a perfectly good pthreads implementation is already available in their toolchain.  
  
This *is* a Boost.Thread issue, it lives in your build files, and it is *not* invalid.

---

## Comment 1

> Username: viboes  
> Created at: 2017-09-21 17:02:54 UTC  
> Url: https://github.com/boostorg/thread/issues/157#issuecomment-331219507  

See #156.
