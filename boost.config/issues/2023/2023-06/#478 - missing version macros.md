# #478 - missing version macros [Open]

> Username: gpeterhoff  
> Created at: 2023-06-15 20:33:50 UTC  
> Updated at: 2023-06-15 20:36:48 UTC  
> Url: https://github.com/boostorg/config/issues/478  

Hello,  
I miss these macros in boost/config.hpp:  
- BOOST_NO_CXX20/BOOST_NO_CXX23 (like BOOST_NO_CXX14, BOOST_NO_CXX17)  
- BOOST_CXX17_CONSTEXPR/BOOST_CXX20_CONSTEXPR/BOOST_CXX23_CONSTEXPR (like BOOST_CXX14_CONSTEXPR)  
- BOOST_CXX20_CONSTEVAL/BOOST_CXX23_CONSTEVAL  
  
This could be done like this:  
//	C++17  
#if defined(__cpp_constexpr)  
	#if (__cpp_constexpr < 201603L)  
		#define BOOST_NO_CXX17_CONSTEXPR  
	#endif  
#else  
	#define BOOST_NO_CXX17_CONSTEXPR  
#endif  
  
#if defined(BOOST_NO_CXX17_CONSTEXPR)  
	#define BOOST_CXX17_CONSTEXPR  
#else  
	#define BOOST_CXX17_CONSTEXPR constexpr  
#endif  
  
  
  
//	C++20  
#if (BOOST_CXX_VERSION < 202002L)  
	#define BOOST_NO_CXX20  
#endif  
  
#if defined(__cpp_constexpr)  
	#if (__cpp_constexpr < 202002L)  
		#define BOOST_NO_CXX20_CONSTEXPR  
	#endif  
#else  
	#define BOOST_NO_CXX20_CONSTEXPR  
#endif  
  
#if defined(BOOST_NO_CXX20_CONSTEXPR)  
	#define BOOST_CXX20_CONSTEXPR  
#else  
	#define BOOST_CXX20_CONSTEXPR constexpr  
#endif  
  
#if defined(__cpp_consteval)  
	#if (__cpp_consteval < 201811L)  
		#define BOOST_CXX20_NO_CONSTEVAL  
	#endif  
#else  
	#define BOOST_CXX20_NO_CONSTEVAL  
#endif  
  
#if defined(BOOST_CXX20_NO_CONSTEVAL)  
	#define BOOST_CXX20_CONSTEVAL  
#else  
	#define BOOST_CXX20_CONSTEVAL consteval  
#endif  
  
  
  
//	C++23  
#if (BOOST_CXX_VERSION < 202100L)  
	#define BOOST_NO_CXX23  
#endif  
  
#if defined(__cpp_constexpr)  
	#if (__cpp_constexpr < 202211L)  
		#define BOOST_NO_CXX23_CONSTEXPR  
	#endif  
#else  
	#define BOOST_NO_CXX23_CONSTEXPR  
#endif  
  
#if defined(BOOST_NO_CXX23_CONSTEXPR)  
	#define BOOST_CXX23_CONSTEXPR  
#else  
	#define BOOST_CXX23_CONSTEXPR constexpr  
#endif  
  
#if defined(__cpp_consteval)  
	#if (__cpp_consteval < 202211L)  
		#define BOOST_CXX23_NO_CONSTEVAL  
	#endif  
#else  
	#define BOOST_CXX23_NO_CONSTEVAL  
#endif  
  
#if defined(BOOST_CXX23_NO_CONSTEVAL)  
	#define BOOST_CXX23_CONSTEVAL  
#else  
	#define BOOST_CXX23_CONSTEVAL consteval  
#endif  
  
thx  
Gero
