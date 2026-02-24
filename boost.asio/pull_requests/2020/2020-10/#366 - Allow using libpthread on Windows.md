# #366 Allow using libpthread on Windows [Closed]

> Username: petrutlucian94  
> Created at: 2020-10-29 09:46:42 UTC  
> Updated at: 2020-12-30 01:18:05 UTC  
> Closed at: 2020-12-30 01:18:04 UTC  
> Url: https://github.com/boostorg/asio/pull/366  

Allow using libpthread on Windows  
  
In some cases, using libpthread on Windows is desired, for example  
when porting software that's already using libpthread, avoiding mixing  
the two libraries, which can be troublesome.  
  
This change will allow using libthread when targetting Windows and  
BOOST_ASIO_HAS_PTHREADS is set.  
  
Related change: https://github.com/chriskohlhoff/asio/pull/566  
  
This commit is currently cherry-picked when building Ceph for Windows: https://github.com/ceph/ceph/blob/master/win32_deps_build.sh#L128-L246

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2020-12-30 01:18:04 UTC  
> Url: https://github.com/boostorg/asio/pull/366#issuecomment-752294269  

Fixed in asio 1.18.1 / boost 1.75.

---
