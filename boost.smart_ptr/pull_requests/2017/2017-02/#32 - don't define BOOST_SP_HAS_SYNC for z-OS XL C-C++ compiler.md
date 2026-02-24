# #32 don't define BOOST_SP_HAS_SYNC for z/OS XL C/C++ compiler [Merged]

> Username: pgroke-dt  
> Created at: 2017-02-25 04:50:29 UTC  
> Updated at: 2017-02-25 11:38:34 UTC  
> Merged at: 2017-02-25 11:38:34 UTC  
> Closed at: 2017-02-25 11:38:34 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/32  

The library currently defines BOOST_SP_HAS_SYNC for IBM z/OS XL C/C++, which doesn't support the \_\_sync intrinsics. Adding _&& !defined( \_\_COMPILER\_VER\_\_ )_ to the condition should prevent that.  
  
(According to https://sourceforge.net/p/predef/wiki/Compilers/ _\_\_COMPILER\_VER\_\__ is only defined by IBM z/OS XL C/C++)

---
