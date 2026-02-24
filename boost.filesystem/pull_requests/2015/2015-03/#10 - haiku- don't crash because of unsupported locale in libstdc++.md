# #10 haiku: don't crash because of unsupported locale in libstdc++ [Merged]

> Username: ahmedcharles  
> Created at: 2015-03-17 22:35:24 UTC  
> Updated at: 2015-03-28 00:02:43 UTC  
> Merged at: 2015-03-28 00:02:43 UTC  
> Closed at: 2015-03-28 00:02:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/10  

See https://svn.boost.org/trac/boost/ticket/4688  
We do the same as on Mac OS X and assume the filesystem uses utf-8.  
  
Signed-off-by: Jessica Hamilton jessica.l.hamilton@gmail.com

---
