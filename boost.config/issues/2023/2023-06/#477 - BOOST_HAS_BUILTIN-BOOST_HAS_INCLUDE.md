# #477 - BOOST_HAS_BUILTIN/BOOST_HAS_INCLUDE [Open]

> Username: gpeterhoff  
> Created at: 2023-06-03 10:40:49 UTC  
> Updated at: 2023-06-03 10:40:49 UTC  
> Url: https://github.com/boostorg/config/issues/477  

BOOST_HAS_BUILTIN  
e.g. clang/gcc support __has_builtin. However, you have to check if this is actually available before using it. I think it would be useful to introduce a macro BOOST_HAS_BUILTIN to avoid redundant queries:  
  
#if defined(__has_builtin)  
 #define BOOST_HAS_BUILTIN(ARG) __has_builtin(ARG)  
#else  
 #define BOOST_HAS_BUILTIN(ARG) 0  
#endif  
  
Maybe you can extend this for other compilers.  
  
BOOST_HAS_INCLUDE  
__has_include is only available from C++17 on. Maybe you have an idea how to implement/provide this for older versions, a la  
  
#if defined(__has_include)  
 #define BOOST_HAS_INCLUDE(ARG) __has_include(ARG)  
#else  
 ???  
#endif  
  
thx  
Gero
