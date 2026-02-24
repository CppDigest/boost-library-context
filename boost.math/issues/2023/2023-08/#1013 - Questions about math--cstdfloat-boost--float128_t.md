# #1013 - Questions about math::cstdfloat/boost::float128_t [Open]

> Username: gpeterhoff  
> Created at: 2023-08-15 14:17:11 UTC  
> Updated at: 2023-08-15 14:17:11 UTC  
> Url: https://github.com/boostorg/math/issues/1013  

Hello,  
please see https://godbolt.org/z/3qMfehf6z  
  
It seems that the intel compiler supports several functions:  
- complex: __cXq  
- strtoflt128/quadmath_snprintf  
  
With Linux it works. My questions:  
1) Are the functions also available on Windows (intel compiler) ?  
2) Are the workarounds for cygwin still needed ?  
3) Are there any other restrictions, e.g. regarding compiler versions (boost::math needs C++14 and therefore up-to-date compilers) ?  
  
I can't check this myself, because I have neither Windows nor intel compiler.  
  
thx  
Gero
