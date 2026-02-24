# #49 Add cygwin64 support to wrap_python.hpp [Merged]

> Username: fatso83  
> Created at: 2015-12-09 22:11:07 UTC  
> Updated at: 2016-03-06 20:13:06 UTC  
> Merged at: 2016-03-06 20:13:06 UTC  
> Closed at: 2016-03-06 20:13:06 UTC  
> Url: https://github.com/boostorg/python/pull/49  

This patch adds 64 bit support.   
  
Currently the lack of 64 bit support for Cygwin prevents projects from compiling on Cygwin64 (see Valloric/YouCompleteMe#684). Variations of this fix has been circulating for [a few years](https://github.com/Valloric/YouCompleteMe/issues/66#issuecomment-27879120).  
  
Originally submitted [on the old Trac issue tracker 9 months ago](https://svn.boost.org/trac/boost/ticket/11125).

---

## Comment 1

> Username: c-nixon  
> Created_at: 2015-12-09 22:22:08 UTC  
> Url: https://github.com/boostorg/python/pull/49#issuecomment-163418416  

@fatso83   
Thanks for posting this across! And thanks for leaving my name on it, I was going to do it myself this evening, but you beat me to it.  
  
Unfortunately I no longer have any windows machines available to me to help with any questions (not that I expect too many with such a small patch) so feel free to put your name on the commit if you're in a better position to shepherd it along.

---

## Comment 2

> Username: BorisSchaeling  
> Created_at: 2016-02-27 22:33:04 UTC  
> Url: https://github.com/boostorg/python/pull/49#issuecomment-189739750  

I can confirm that boost/python/detail/wrap_python.hpp still has to be patched to support Boost.Python on Cygwin64 (I'm using Boost 1.60.0).   
  
An alternative patch is wrapping `#define SIZEOF_LONG 4` with an #ifndef:   
  
```  
#ifndef SIZEOF_LONG  
# define SIZEOF_LONG 4  
#endif  
```  
  
This works as Cgwin64 defines SIZEOF_LONG in /usr/include/python2.7/pyconfig.h (which is included by boost/python/detail/wrap_python.hpp).

---
