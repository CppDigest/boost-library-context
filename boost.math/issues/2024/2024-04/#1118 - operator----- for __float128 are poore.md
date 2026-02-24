# #1118 - operator<</>> for __float128 are poore [Open]

> Username: gpeterhoff  
> Created at: 2024-04-07 00:44:19 UTC  
> Updated at: 2024-04-07 01:05:28 UTC  
> Url: https://github.com/boostorg/math/issues/1118  

generally  
- hexfloat is not supported  
- in the implementations, exceptions are used for flow control -> this procedure is/(has never been) sensible  
  
Suggestions  
a) via quadmath_snprintf/strtoflt128  
- for gcc the operators can be updated  
- this may also be possible for intel  
 1. reasonably current intel compilers also seem to provide quadmath_snprintf/strtoflt128. Is that correct? Then you can use these functions.  
 2. What relevance does __float128/_Quad still have for intel? This could then be removed if necessary.  
  
b) via std/boost to_chars/from_chars  
- std  
 1. If std::float128_t is available, these functions can be used  
 2. Possibly these functions are still implemented in gcc for __float128 https://gcc.gnu.org/bugzilla/show_bug.cgi?id=113260  
- boost  
 With boost 1.85 there is boost::charconv, but the functions are **incompatible** with the standard https://www.boost.org/doc/libs/1_85_0_beta1/libs/charconv/doc/html/charconv.html#to_chars_usage_notes_for_to_chars_for_floating_point_types  
 Matt unfortunately refuses to implement them in a compatible way :-(   
So the NaN cases would have to be handled manually.  
  
regards  
Gero
