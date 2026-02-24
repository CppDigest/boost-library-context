# #37 Ported predef file for Windows Phone 8.1 [Closed]

> Username: mosherubin  
> Created at: 2015-11-03 19:44:27 UTC  
> Updated at: 2015-11-15 02:41:40 UTC  
> Closed at: 2015-11-15 02:41:40 UTC  
> Url: https://github.com/boostorg/predef/pull/37  



---

## Comment 1

> Username: grafikrobot  
> Created_at: 2015-11-03 20:24:04 UTC  
> Url: https://github.com/boostorg/predef/pull/37#issuecomment-153476345  

I don't see what you are trying to fix with this. The BOOST_PLAT_WINDOWS_RUNTIME symbol should already only get defined once as the file is inclusion guarded.

---

## Comment 2

> Username: mosherubin  
> Created_at: 2015-11-03 20:50:41 UTC  
> Updated_at: 2015-11-04 03:59:23 UTC  
> Url: https://github.com/boostorg/predef/pull/37#issuecomment-153484340  

@grafikrobot My change here might not be necessary.  It was necessary when I ported Boost 1.57, but the need may have dissipated by 1.59.  If you feel it is not necessary, please ignore it.  Thanks!

---
