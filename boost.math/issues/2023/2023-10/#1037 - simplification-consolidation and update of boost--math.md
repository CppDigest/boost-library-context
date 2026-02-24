# #1037 - simplification/consolidation and update of boost::math [Closed]

> Username: gpeterhoff  
> Created at: 2023-10-13 15:00:20 UTC  
> Updated at: 2024-02-05 09:27:34 UTC  
> Closed at: 2024-02-05 09:27:34 UTC  
> Url: https://github.com/boostorg/math/issues/1037  

Hi,  
  
1) boost/type_traits/is_floating_point.hpp  
does not support std::floatX_t  
  
2) boost/config/detail/cxx_composite.hpp does not support C++23   
[cxx_composite.hpp.txt](https://github.com/boostorg/math/files/12894526/cxx_composite.hpp.txt)  
  
3) boost/config/detail/suffix.hpp + boost/math/tools/config.hpp  
current:  
In suffix.hpp only rudimentary check if float128 is available. In boost/math/tools/config.hpp the exact type is first specified (__float128/_Quad). For this the macros BOOST_MATH_USE_FLOAT128 and BOOST_MATH_FLOAT128_TYPE are introduced. This has the consequence that all building libraries must struggle with it.  
makes sense:  
- provide the correct float128 type in boost/config/detail/suffix.hpp [suffix.hpp.txt](https://github.com/boostorg/math/files/12894553/suffix.hpp.txt)  
- in boost/math/tools/config.hpp the sections #ifdef BOOST_MATH_USE_FLOAT128 (and probably for BOOST_MATH_USE_FLOAT128) can be removed. [config.hpp.txt](https://github.com/boostorg/math/files/12894570/config.hpp.txt)  
  
Based on this, quite a few things can be simplified:  
- boost/math/cstdfloat/cstdfloat_types.hpp (supports std::floatX_t) [cstdfloat_types.hpp.txt](https://github.com/boostorg/math/files/12894597/cstdfloat_types.hpp.txt)  
- boost/math/cstdfloat/cstdfloat_complex.hpp [cstdfloat_complex.hpp.txt](https://github.com/boostorg/math/files/12894606/cstdfloat_complex.hpp.txt)  
- [cstdfloat_cquadmath.hpp.txt](https://github.com/boostorg/math/files/12894648/cstdfloat_cquadmath.hpp.txt)  
  
These are now only excerpts of what I mean. For example, were still missing here stream<</>> (have already done, but would lead too far here).  
  
thx  
Gero

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-10-15 15:45:34 UTC  
> Url: https://github.com/boostorg/math/issues/1037#issuecomment-1763429041  

>boost/type_traits/is_floating_point.hpp  
does not support std::floatX_t  
  
Fixed in develop.  
  
>boost/config/detail/cxx_composite.hpp does not support C++23  
[cxx_composite.hpp.txt](https://github.com/boostorg/math/files/12894526/cxx_composite.hpp.txt)  
  
Yes it does, see: https://github.com/boostorg/config/blob/54afd34839eaa9ebdfa7c345d18d7481f4ce83c1/include/boost/config/detail/cxx_composite.hpp#L205  
  
>boost/config/detail/suffix.hpp + boost/math/tools/config.hpp  
current:  
In suffix.hpp only rudimentary check if float128 is available. In boost/math/tools/config.hpp the exact type is first specified (__float128/_Quad). For this the macros BOOST_MATH_USE_FLOAT128 and BOOST_MATH_FLOAT128_TYPE are introduced.  
  
I'm not sure I agree with this, what Boost.Math needs from Config is quite different to what other libraries may need.  BOOST_HAS_FLOAT128 is quite specifically about the GCC/Clang extension type `__float128` , `std::float128` is a quite distinct type and the two should not be conflated - for example type_traits needs to know about *both* types, likewise any other library that is providing either specializations or overloads on them.  Boost.Math is generally different in that we deal in one high level type (at least as far as cstdfloat.hpp is concerned), so it makes sense for us to define our own macros for handling that IMO.

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2023-10-23 16:02:33 UTC  
> Url: https://github.com/boostorg/math/issues/1037#issuecomment-1775525302  

Hello John,  
> > boost/type_traits/is_floating_point.hpp  
> > does not support std::floatX_t  
>   
> Fixed in develop.  
>   
> > boost/config/detail/cxx_composite.hpp does not support C++23  
> > [cxx_composite.hpp.txt](https://github.com/boostorg/math/files/12894526/cxx_composite.hpp.txt)  
>   
> Yes it does, see: https://github.com/boostorg/config/blob/54afd34839eaa9ebdfa7c345d18d7481f4ce83c1/include/boost/config/detail/cxx_composite.hpp#L205  
>   
Fine.  
> > boost/config/detail/suffix.hpp + boost/math/tools/config.hpp  
> > current:  
> > In suffix.hpp only rudimentary check if float128 is available. In boost/math/tools/config.hpp the exact type is first specified (__float128/_Quad). For this the macros BOOST_MATH_USE_FLOAT128 and BOOST_MATH_FLOAT128_TYPE are introduced.  
>   
> I'm not sure I agree with this, what Boost.Math needs from Config is quite different to what other libraries may need. BOOST_HAS_FLOAT128 is quite specifically about the GCC/Clang extension type `__float128` , `std::float128` is a quite distinct type and the two should not be conflated - for example type_traits needs to know about _both_ types, likewise any other library that is providing either specializations or overloads on them. Boost.Math is generally different in that we deal in one high level type (at least as far as cstdfloat.hpp is concerned), so it makes sense for us to define our own macros for handling that IMO.  
  
That's all clear. What bothers me and is prone to errors:  
boost/config/detail/suffix.hpp:  
```  
// same again for __float128:  
#if defined(BOOST_HAS_FLOAT128) && defined(__cplusplus)  
namespace boost {  
# ifdef __GNUC__  
    __extension__ typedef __float128 float128_type;  
# else  
    typedef __float128 float128_type;  
# endif  
}  
#endif  
```  
If BOOST_HAS_FLOAT128 is defined, it is assumed at this point that the extended FP128 type is __float128. However, in the Windows+intel-compiler case, the extended FP128 type is _Quad.  
Only done later  
  boost/math/tools/config.hpp:  
```  
#ifdef BOOST_MATH_USE_FLOAT128  
//  
// Only enable this when the compiler really is GCC as clang and probably  
// intel too don't support __float128 yet :-(  
//  
# if defined(__INTEL_COMPILER) && defined(__GNUC__)  
# if (__GNUC__ > 4) || ((__GNUC__ == 4) && (__GNUC_MINOR__ >= 6))  
# define BOOST_MATH_FLOAT128_TYPE __float128  
# endif  
# elif defined(__GNUC__)  
# define BOOST_MATH_FLOAT128_TYPE __float128  
# endif  
  
# ifndef BOOST_MATH_FLOAT128_TYPE  
# define BOOST_MATH_FLOAT128_TYPE _Quad  
# endif  
#endif  
```  
and boost/math/cstdfloat/cstdfloat_types.hpp:  
```  
#if (BOOST_CSTDFLOAT_HAS_FLOAT128_NATIVE_TYPE == 0) && defined(BOOST_MATH_USE_FLOAT128) && !defined(BOOST_CSTDFLOAT_NO_LIBQUADMATH_SUPPORT)  
     // Specify the underlying name of the internal 128-bit floating-point type definition.  
     namespace boost { namespace math { namespace cstdfloat { namespace detail {  
     #if defined(__GNUC__)  
       typedef __float128 float_internal128_t;  
     #elif defined(__INTEL_COMPILER)  
       typedef _Quad float_internal128_t;  
     #else  
       #error "Sorry, the compiler is neither GCC, nor Intel, I don't know how to configure <boost/cstdfloat.hpp>."  
     #endif  
     } } } } // boost::math::cstdfloat::detail  
```  
This is confusing and messy because the correct extended FP128 type is now double wide: BOOST_MATH_FLOAT128_TYPE **and** float_internal128_t.  
  
Therefore it would be better to specify the correct type at a single point (boost/config/detail/suffix.hpp):  
```  
// same again for __float128:  
#if defined(BOOST_HAS_FLOAT128) && defined(__cplusplus)  
  
#if (BOOST_INTEL && BOOST_WINDOWS)  
// https://community.intel.com/t5/Intel-C-Compiler/C-Compiler-128-bit-floating-point/m-p/1216949  
typedef _Quad __float128;  
#endif  
  
namespace boost  
{  
#if BOOST_GCC  
__extension__ typedef __float128 float128_type;  
#else  
    typedef __float128 float128_type;  
#endif  
}  
#endif  
```  
In the event that a library (mistakenly) always uses __float128, the typedef is used for Win+intel.  
This ensures that boost::float128_type is always the correct extended FP128 type. The define in boost/math/tools/config.hpp (BOOST_MATH_FLOAT128_TYPE) and typedef in boost/math/cstdfloat/cstdfloat_types.hpp (float_internal128_t) can be removed; also the superfluous BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE.  
  
thx  
Gero

---

## Comment 3

> Username: gpeterhoff  
> Created at: 2023-10-23 19:21:38 UTC  
> Url: https://github.com/boostorg/math/issues/1037#issuecomment-1775875896  

With my considerations I still forgot that the case long double==FP128 must still be considered. What a plague...

---

## Comment 4

> Username: jzmaddock  
> Created at: 2023-10-24 15:18:39 UTC  
> Url: https://github.com/boostorg/math/issues/1037#issuecomment-1777463373  

No, you're missing the point, BOOST_HAS_FLOAT128 relates to nothing except the type `__float128`, it tells you nothing about `_Quad` or any other type, nor is it meant to.  This is exactly the information that type_traits and other libraries that need to deal with the underlying types require. <stdfloat> and <boost/cstdfloat.hpp> are higher level than that.  So... Config provides the low level information about what types are available, and then cstdfloat.hpp uses that to create the high level information.
