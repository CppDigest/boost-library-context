# #519 - add C++26 version/header marcos [Open]

> Username: gpeterhoff  
> Created at: 2025-07-23 17:15:45 UTC  
> Updated at: 2025-07-23 17:15:45 UTC  
> Url: https://github.com/boostorg/config/issues/519  

Please add support for C++26 in boost/config/detail. I hope it is not too late for 1.89.  
  
cxx_composite.hpp  
```  
#if defined(BOOST_NO_CXX23)\  
   || defined(BOOST_NO_CXX26_HDR_CONTRACTS)\  
   || defined(BOOST_NO_CXX26_HDR_DEBUGGING)\  
   || defined(BOOST_NO_CXX26_HDR_HAZARD_POINTER)\  
   || defined(BOOST_NO_CXX26_HDR_HIVE)\  
   || defined(BOOST_NO_CXX26_HDR_INPLACE_VECTOR)\  
   || defined(BOOST_NO_CXX26_HDR_LINALG)\  
   || defined(BOOST_NO_CXX26_HDR_RCU)\  
   || defined(BOOST_NO_CXX26_HDR_SIMD)\  
   || defined(BOOST_NO_CXX26_HDR_TEXT_ENCODING)  
#    define BOOST_NO_CXX26  
#endif  
```  
  
suffix.hpp  
```  
#if (!defined(__has_include) || (BOOST_CXX_VERSION < 202400L))  
#  define BOOST_NO_CXX26_HDR_CONTRACTS  
#  define BOOST_NO_CXX26_HDR_DEBUGGING  
#  define BOOST_NO_CXX26_HDR_HAZARD_POINTER  
#  define BOOST_NO_CXX26_HDR_HIVE  
#  define BOOST_NO_CXX26_HDR_INPLACE_VECTOR  
#  define BOOST_NO_CXX26_HDR_LINALG  
#  define BOOST_NO_CXX26_HDR_RCU  
#  define BOOST_NO_CXX26_HDR_SIMD  
#  define BOOST_NO_CXX26_HDR_TEXT_ENCODING  
#else  
#if (!__has_include(<contracts>) || !defined(__cpp_lib_contracts) || (__cpp_lib_contracts < 202502L)) && !defined(BOOST_NO_CXX26_HDR_CONTRACTS)  
#  define BOOST_NO_CXX26_HDR_CONTRACTS  
#endif  
#if (!__has_include(<debugging>) || !defined(__cpp_lib_debugging) || (__cpp_lib_debugging < 202311L)) && !defined(BOOST_NO_CXX26_HDR_DEBUGGING)  
#  define BOOST_NO_CXX26_HDR_DEBUGGING  
#endif  
#if (!__has_include(<hazard_pointer>) || !defined(__cpp_lib_hazard_pointer) || (__cpp_lib_hazard_pointer < 202306L)) && !defined(BOOST_NO_CXX26_HDR_HAZARD_POINTER)  
#  define BOOST_NO_CXX26_HDR_HAZARD_POINTER  
#endif  
#if (!__has_include(<hive>) || !defined(__cpp_lib_hive  ) || (__cpp_lib_hive  < 202502L)) && !defined(BOOST_NO_CXX26_HDR_HIVE)  
#  define BOOST_NO_CXX26_HDR_HIVE  
#endif  
#if (!__has_include(<inplace_vector>) || !defined(__cpp_lib_inplace_vector  ) || (__cpp_lib_inplace_vector < 202406L)) && !defined(BOOST_NO_CXX26_HDR_INPLACE_VECTOR)  
#  define BOOST_NO_CXX26_HDR_INPLACE_VECTOR  
#endif  
#if (!__has_include(<linalg>) || !defined(__cpp_lib_linalg) || (__cpp_lib_linalg < 202311L)) && !defined(BOOST_NO_CXX26_HDR_LINALG)  
#  define BOOST_NO_CXX26_HDR_LINALG  
#endif  
#if (!__has_include(<rcu>) || !defined(__cpp_lib_rcu) || (__cpp_lib_rcu < 202306L)) && !defined(BOOST_NO_CXX26_HDR_RCU)  
#  define BOOST_NO_CXX26_HDR_RCU  
#endif  
#if (!__has_include(<simd>) || !defined(__cpp_lib_simd) || (__cpp_lib_simd < 202411L)) && !defined(BOOST_NO_CXX26_HDR_SIMD)  
#  define BOOST_NO_CXX26_HDR_SIMD  
#endif  
#if (!__has_include(<text_encoding>) || !defined(__cpp_lib_text_encoding) || (__cpp_lib_text_encoding < 202306L)) && !defined(BOOST_NO_CXX26_HDR_TEXT_ENCODING)  
#  define BOOST_NO_CXX26_HDR_TEXT_ENCODING  
#endif  
#endif  
```  
  
thx  
Gero
