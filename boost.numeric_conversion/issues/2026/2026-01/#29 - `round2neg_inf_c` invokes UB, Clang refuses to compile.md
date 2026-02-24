# #29 - `round2neg_inf_c` invokes UB, Clang refuses to compile [Open]

> Username: intractabilis  
> Created at: 2026-01-15 01:15:17 UTC  
> Updated at: 2026-01-15 01:15:17 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/29  

https://github.com/boostorg/numeric_conversion/blob/d1b479f7a4aa54d8ffb93d8dc4ee0c24670210d8/include/boost/numeric/conversion/detail/converter.hpp#L29  
This invokes undefined behavior. Clang correctly identifies it and refuses to compile if you try to instantiate `round2neg_inf_c`. Here is an MWE (verified with Boost 1.89-1.90)  
```  
$ clang++ --version  
clang version 21.1.6  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
$ clang++ -c -x c++ - <<EOF  
#include <boost/numeric/conversion/converter.hpp>  
  
int main (int argc, char *argv[]) {  
    boost::numeric::convdetail::round2neg_inf_c x;  
}  
EOF  
In file included from <stdin>:1:  
In file included from /usr/include/boost/numeric/conversion/converter.hpp:13:  
In file included from /usr/include/boost/numeric/conversion/conversion_traits.hpp:13:  
In file included from /usr/include/boost/numeric/conversion/detail/conversion_traits.hpp:21:  
In file included from /usr/include/boost/numeric/conversion/detail/is_subranged.hpp:17:  
In file included from /usr/include/boost/mpl/multiplies.hpp:17:  
In file included from /usr/include/boost/mpl/times.hpp:19:  
In file included from /usr/include/boost/mpl/aux_/arithmetic_op.hpp:17:  
In file included from /usr/include/boost/mpl/integral_c.hpp:32:  
/usr/include/boost/mpl/aux_/integral_wrapper.hpp:62:51: error: in-class initializer for static data member is not a  
      constant expression  
   62 |     BOOST_STATIC_CONSTANT(AUX_WRAPPER_VALUE_TYPE, next_value = BOOST_MPL_AUX_STATIC_CAST(AUX_WRAPPER_VALUE_TYPE, (N + 1)));  
      |     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/config/detail/suffix.hpp:410:72: note: expanded from macro 'BOOST_STATIC_CONSTANT'  
  410 | #     define BOOST_STATIC_CONSTANT(type, assignment) static const type assignment  
      |                                                                        ^~~~~~~~~~  
<stdin>:4:49: note: in instantiation of template class 'mpl_::integral_c<std::float_round_style,  
      std::round_toward_neg_infinity>' requested here  
    4 |     boost::numeric::convdetail::round2neg_inf_c x;  
      |                                                 ^  
/usr/include/boost/mpl/aux_/integral_wrapper.hpp:62:64: note: integer value 4 is outside the valid range of values  
      [-4, 3] for the enumeration type 'float_round_style'  
   62 |     BOOST_STATIC_CONSTANT(AUX_WRAPPER_VALUE_TYPE, next_value = BOOST_MPL_AUX_STATIC_CAST(AUX_WRAPPER_VALUE_TYPE, (N + 1)));  
      |                                                                ^  
/usr/include/boost/mpl/aux_/static_cast.hpp:24:47: note: expanded from macro 'BOOST_MPL_AUX_STATIC_CAST'  
   24 | #   define BOOST_MPL_AUX_STATIC_CAST(T, expr) static_cast<T>(expr)  
      |                                               ^  
1 error generated.  
```  
It appears that Clang reserves 3 bits for `std::float_round_style`. This makes `std::round_toward_neg_infinity + 1` to overflow.
