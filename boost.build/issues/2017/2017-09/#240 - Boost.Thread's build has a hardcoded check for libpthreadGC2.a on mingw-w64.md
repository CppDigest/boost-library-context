# #240 - Boost.Thread's build has a hardcoded check for libpthreadGC2.a on mingw-w64 [Closed]

> Username: hanetzer  
> Created at: 2017-09-22 00:06:26 UTC  
> Updated at: 2017-09-22 17:42:06 UTC  
> Closed at: 2017-09-22 17:42:06 UTC  
> Url: https://github.com/boostorg/build/issues/240  

On mingw-w64, one can build the runtime with pthread libraries, which work in the same manner as the standard one from glibc/musl/etc, and as such requiring the installation of some external dependency instead of using the perfectly good and usable libpthread.dll.a/libpthread.a import libraries and static archive is no good.

---

## Comment 1

> Username: viboes  
> Created at: 2017-09-22 17:32:25 UTC  
> Url: https://github.com/boostorg/build/issues/240#issuecomment-331511364  

See https://github.com/boostorg/thread/issues/156.  
  
Sorry, I don't know why I believes the Jamfile you mentioned in #156 was from the Boost.Build. It is from Boost.Thread so I propose to close this issue.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2017-09-22 17:42:06 UTC  
> Url: https://github.com/boostorg/build/issues/240#issuecomment-331514292  

OK, closing it as not actually relevant to B2.
