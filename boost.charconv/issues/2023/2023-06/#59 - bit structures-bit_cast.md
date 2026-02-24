# #59 - bit structures/bit_cast [Closed]

> Username: gpeterhoff  
> Created at: 2023-06-05 14:33:20 UTC  
> Updated at: 2023-06-05 17:01:59 UTC  
> Closed at: 2023-06-05 14:50:14 UTC  
> Url: https://github.com/boostorg/charconv/issues/59  

Hello Matt,  
I see you are implementing the bit structures twice:  
https://github.com/cppalliance/charconv/blob/develop/include/boost/charconv/detail/bit_layouts.hpp  
https://github.com/boostorg/math/blob/develop/include/boost/math/ccmath/signbit.hpp  
  
I think it is better to do this only once, for example in boost::math::bits:  
[bits.hpp.txt](https://github.com/cppalliance/charconv/files/11653578/bits.hpp.txt)  
In detail I first provide various bit structures, no matter if they are needed or not. In bits the "normal" bit structures are available.  
  
In https://github.com/boostorg/math/blob/develop/include/boost/math/ccmath/signbit.hpp you still implement BOOST_MATH_BIT_CAST. This is not necessary anymore, because Peter has adapted boost::core::bit_cast   
https://github.com/boostorg/core/blob/develop/include/boost/core/bit.hpp  
https://github.com/boostorg/core/issues/147 (not quite correct yet)  
  
thx & regards  
Gero

---

## Comment 1

> Username: mborland  
> Created at: 2023-06-05 14:50:14 UTC  
> Url: https://github.com/boostorg/charconv/issues/59#issuecomment-1576948094  

I am unable to use your implementation, or the one from Boost.Math because this is a C++11 library and `<boost/math/special_functions/detail/fp_traits.hpp>` requires C++14. I could move the definitions to a standalone file in Boost.Math, but that is a heavy dependency for a few macros. I am not super worried about code duplication in this instance for a small number of well defined values that are unlikely to change.

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2023-06-05 17:01:58 UTC  
> Url: https://github.com/boostorg/charconv/issues/59#issuecomment-1577156256  

Instead of <fp_traits.hpp> we can use <boost/predef/other/endian.h> directly and in the bit structures:  
  
struct X_bits  
{  
#if defined(BOOST_ENDIAN_LITTLE_BYTE_AVAILABLE)  
#else  
#endif  
};  
  
This makes more sense anyway and the code duplication can be omitted.
