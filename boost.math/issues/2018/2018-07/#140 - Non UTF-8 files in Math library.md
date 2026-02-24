# #140 - Non UTF-8 files in Math library [Closed]

> Username: jzmaddock  
> Created at: 2018-07-31 17:55:09 UTC  
> Updated at: 2018-08-18 18:37:27 UTC  
> Closed at: 2018-08-18 18:37:27 UTC  
> Url: https://github.com/boostorg/math/issues/140  

The Math library contains two non UTF-8 files (in both cases a comment which contains a Windows CP1252 dash).  
  
Here the output of the "file" command:  
  
boost/math/special_functions/detail/bessel_i0.hpp: C++ source, Non-ISO extended-ASCII text boost/math/special_functions/detail/bessel_i1.hpp: C++ source, Non-ISO extended-ASCII text  
  
This makes the files incompatible with modern Microsoft Compilers when using switch /utf-8.  
  
The attached patch simply replaces the CP1252 dash with an ASCII dash and therefore fixes the issue for all compilers.  
  
  
Moved from Trac.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-18 18:37:27 UTC  
> Url: https://github.com/boostorg/math/issues/140#issuecomment-414078137  

Already fixed in develop.
