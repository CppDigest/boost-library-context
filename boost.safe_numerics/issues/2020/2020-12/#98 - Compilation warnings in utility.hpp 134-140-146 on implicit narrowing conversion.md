# #98 - Compilation warnings in utility.hpp 134/140/146 on implicit narrowing conversion [Closed]

> Username: PeterSommerlad  
> Created at: 2020-12-28 13:23:21 UTC  
> Updated at: 2020-12-28 19:24:01 UTC  
> Closed at: 2020-12-28 19:24:01 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/98  

All theses cases while technically correct code, generate compiler warnings on gcc.  
  
```  
        const boost::uint_t<8>::exact upper_half = (t >> 8);  
  
/usr/local/include/boost/safe_numerics/utility.hpp:134:55: warning: conversion from 'int' to 'boost::detail::exact_unsigned_base_helper<8>::exact' {aka 'unsigned char'} may change value [-Wconversion]  
```  
  
A potential fix would be a static_cast of the rhs to the target type of the  lhs. Also note, that due to integral promotion (unsigned short -> int), the shift occurs as signed.  
  
I am not sure if a perfect fix, but I would try  
  
```  
        const boost::uint_t<8>::exact upper_half = static_cast<boost::uint_t<8>::exact>(t >> 8u);  
```  
  
to silence the warning and the other two cases for 16 and 32bit accordingly.

---

## Comment 1

> Username: robertramey  
> Created at: 2020-12-28 19:24:01 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/98#issuecomment-751836078  

I'm generally a little careful about making changes just to silence warnings.  Sometimes one ends up silencing too much! And of course it means results vary by compiler.  But in this case, I noticed that in this case, I've already static_cast for this very purpose.  So I guess it makes sense to do it in this case as well.  Good call.
