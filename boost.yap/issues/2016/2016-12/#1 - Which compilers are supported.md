# #1 - Which compilers are supported? [Closed]

> Username: ldionne  
> Created at: 2016-12-18 04:51:46 UTC  
> Updated at: 2018-06-11 17:16:17 UTC  
> Closed at: 2016-12-20 00:52:17 UTC  
> Url: https://github.com/boostorg/yap/issues/1  

It won't build with Clang 3.8 and GCC 6. It would be nice to document which minimum versions are required.

---

## Comment 1

> Username: tzlaine  
> Created at: 2016-12-18 21:56:21 UTC  
> Url: https://github.com/boostorg/yap/issues/1#issuecomment-267849743  

This is an excellent point.  I'll add docs that indicate this, but Clang 3.8+ and GCC 6+ should both work.  Are you using the c++14 branch?  That's the most usable one.  The master branch might only work with Clang-SVN (that's all I've tried).

---

## Comment 2

> Username: ldionne  
> Created at: 2016-12-18 21:57:07 UTC  
> Url: https://github.com/boostorg/yap/issues/1#issuecomment-267849787  

Oh, I'm doing everything on master.
