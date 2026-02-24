# #74 - compilation fails with clang 16 when enumerated type used in AUX_WRAPPER_INST [Closed]

> Username: yuvalif  
> Created at: 2022-11-15 10:04:30 UTC  
> Updated at: 2023-06-11 00:00:51 UTC  
> Closed at: 2022-11-16 09:15:38 UTC  
> Url: https://github.com/boostorg/mpl/issues/74  

using boost 1.79 and clang 16 I get the following compilation errors when trying to define the next/prev types of an enumerated type in the range [0,3]:  
```  
In file included from libs/python/src/converter/builtin_converters.cpp:21:  
In file included from ./boost/cast.hpp:18:  
In file included from ./boost/numeric/conversion/cast.hpp:33:  
In file included from ./boost/numeric/conversion/converter.hpp:13:  
In file included from ./boost/numeric/conversion/conversion_traits.hpp:13:  
In file included from ./boost/numeric/conversion/detail/conversion_traits.hpp:18:  
In file included from ./boost/numeric/conversion/detail/int_float_mixture.hpp:19:  
In file included from ./boost/mpl/integral_c.hpp:32:  
./boost/mpl/aux_/integral_wrapper.hpp:73:31: error: integer value -1 is outside the valid range of values [0, 3] for this enumeration type [-Wenum-constexpr-conversion]  
./boost/mpl/aux_/static_cast.hpp:24:47: note: expanded from macro 'BOOST_MPL_AUX_STATIC_CAST'  
#   define BOOST_MPL_AUX_STATIC_CAST(T, expr) static_cast<T>(expr)  
```  
```  
In file included from libs/python/src/converter/builtin_converters.cpp:21:  
In file included from ./boost/cast.hpp:18:  
In file included from ./boost/numeric/conversion/cast.hpp:33:  
In file included from ./boost/numeric/conversion/converter.hpp:13:  
In file included from ./boost/numeric/conversion/conversion_traits.hpp:13:  
In file included from ./boost/numeric/conversion/detail/conversion_traits.hpp:18:  
In file included from ./boost/numeric/conversion/detail/int_float_mixture.hpp:19:  
In file included from ./boost/mpl/integral_c.hpp:32:  
./boost/mpl/aux_/integral_wrapper.hpp:72:31: error: integer value 4 is outside the valid range of values [0, 3] for this enumeration type [-Wenum-constexpr-conversion]  
    typedef AUX_WRAPPER_INST( BOOST_MPL_AUX_STATIC_CAST(AUX_WRAPPER_VALUE_TYPE, (value + 1)) ) next;  
                              ^  
./boost/mpl/aux_/static_cast.hpp:24:47: note: expanded from macro 'BOOST_MPL_AUX_STATIC_CAST'  
#   define BOOST_MPL_AUX_STATIC_CAST(T, expr) static_cast<T>(expr)  
                                              ^  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2022-11-16 08:46:31 UTC  
> Url: https://github.com/boostorg/mpl/issues/74#issuecomment-1316613560  

See https://github.com/boostorg/mpl/issues/69 and https://github.com/boostorg/numeric_conversion/issues/24.

---

## Comment 2

> Username: yuvalif  
> Created at: 2022-11-16 09:15:38 UTC  
> Url: https://github.com/boostorg/mpl/issues/74#issuecomment-1316653921  

@pdimov, thanks!  
will close in favor of the other one.

---

## Comment 3

> Username: dkrejsa  
> Created at: 2023-06-10 23:31:21 UTC  
> Url: https://github.com/boostorg/mpl/issues/74#issuecomment-1585859774  

Hi,  
Compiling Boost 1.81 with clang 16.0.0, I still see this sort of error:  
  
> clang-vxworks.compile.c++ ../bin.v2/libs/numeric/conversion/test/traits_test.test/clang-vxworks-16.0.0.1/debug/static-only-on/target-os-vxworks/traits_test.o  
> In file included from ../libs/numeric/conversion/test/traits_test.cpp:19:  
> In file included from ../boost/numeric/conversion/conversion_traits.hpp:13:  
> In file included from ../boost/numeric/conversion/detail/conversion_traits.hpp:21:  
> In file included from ../boost/numeric/conversion/detail/is_subranged.hpp:17:  
> In file included from ../boost/mpl/multiplies.hpp:17:  
> In file included from ../boost/mpl/times.hpp:19:  
> In file included from ../boost/mpl/aux_/arithmetic_op.hpp:17:  
> In file included from ../boost/mpl/integral_c.hpp:32:  
> ../boost/mpl/aux_/integral_wrapper.hpp:73:31: error: integer value -1 is outside the valid range of values [0, 3] for this enumeration type [-Wenum-constexpr-conversion]  
>     typedef AUX_WRAPPER_INST( BOOST_MPL_AUX_STATIC_CAST(AUX_WRAPPER_VALUE_TYPE, (value - 1)) ) prior;  
>                               ^  
> ../boost/mpl/aux_/static_cast.hpp:24:47: note: expanded from macro 'BOOST_MPL_AUX_STATIC_CAST'  
> \#   define BOOST_MPL_AUX_STATIC_CAST(T, expr) static_cast<T>(expr)  
>                                               ^  
>  
  
The change https://github.com/boostorg/numeric_conversion/commit/50a1eae942effb0a9b90724323ef8f2a67e7984a  
is already present.   Any ideas why it might not be effective in preventing the compile error in my environment?  
I am using somewhat uncommon standard C++ libraries (from VxWorks, modified Dinkum libraries), but it looks like most of the issue is inside Boost headers...

---

## Comment 4

> Username: pdimov  
> Created at: 2023-06-11 00:00:51 UTC  
> Url: https://github.com/boostorg/mpl/issues/74#issuecomment-1585864678  

Hard to say. I tried it on Compiler Explorer and it worked: https://godbolt.org/z/dhrscKoz1.
