# #84 - conflict with <iso646.h> [Closed]

> Username: OgreTransporter  
> Created at: 2025-02-02 09:34:12 UTC  
> Updated at: 2025-06-26 23:46:35 UTC  
> Closed at: 2025-06-26 23:46:35 UTC  
> Url: https://github.com/boostorg/mpl/issues/84  

I have just checked out and compiled the latest boost version. Now I'm trying to compile SOCI with it. I have noticed an error.  
  
```  
boost\mpl\aux_\include_preprocessed.hpp(39,13): Error C1083: File (include) cannot be opened: “boost/mpl/aux_/preprocessed/plain/&&.hpp”: No such file or directory  
```  
  
https://github.com/boostorg/mpl/blob/cd74f40d46321209b26ab516e044cda0f32d1fdf/include/boost/mpl/and.hpp#L41  
  
The `and` in `and.hpp` is replaced by `&&`.  
  
```c++  
// agurt, 19/may/04: workaround a conflict with <iso646.h> header's   
// 'or' and 'and' macros, see http://tinyurl.com/3et69; 'defined(and)'  
// has to be checked in a separate condition, otherwise GCC complains   
// about 'and' being an alternative token  
#if defined(_MSC_VER) && !defined(__cplusplus)  
#ifndef __GCCXML__  
#if defined(and)   
#   pragma push_macro("and")  
#   undef and  
#   define and(x)  
#endif  
#endif  
#endif  
  
#   define BOOST_MPL_PREPROCESSED_HEADER and.hpp  
#   include <boost/mpl/aux_/include_preprocessed.hpp>  
  
#if defined(_MSC_VER) && !defined(__clang__) && !defined(__cplusplus)  
#ifndef __GCCXML__  
#if defined(and)   
#   pragma pop_macro("and")  
#endif  
#endif  
#endif  
```  
_MSC_VER = 1942  
__cplusplus = 199711L  
  
`__GCCXML__` and `__clang__` are not set.  
  
Edit: With SOCI it helped to deactivate the macro `_MSC_EXTENSIONS` via compiler flag.

---

## Comment 1

> Username: luiskutschat  
> Created at: 2025-02-19 13:16:19 UTC  
> Updated at: 2025-02-19 13:18:21 UTC  
> Url: https://github.com/boostorg/mpl/issues/84#issuecomment-2668626973  

I had the same issue while trying to build symengine (https://github.com/symengine/symengine) with boost (CMake option INTEGER_CLASS=boostmp) with MSVC compiler.  
  
In symengine \<ciso646\> is included, which includes \<iso646\>.  
  
For me, it worked to comment out "&& !defined(__cplusplus)" in the corresponding lines in mpl/include/boost/mpl/and.hpp, or.hpp, bitand.hpp and bitor.hpp.  
  
Thus, "#pragma push_macro("and")" and "#pragma pop_macro("and")" are actually executed because for me it was the case that the macro __cplusplus is defined.  
  
"#pragma push_macro("and")" is needed so that BOOST_MPL_PREPROCESSED_HEADER is "and.hpp" instead of "&&.hpp", which is then used in mpl/aux_/include_preprocessed.hpp.  
  
I am not completely sure, but maybe it has to be "&& defined(__cplusplus)" (so no "!") in mpl/include/boost/mpl/and.hpp, or.hpp, bitand.hpp and bitor.hpp?

---

## Comment 2

> Username: egorpugin  
> Created at: 2025-04-11 13:09:21 UTC  
> Url: https://github.com/boostorg/mpl/issues/84#issuecomment-2796876352  

same issue
