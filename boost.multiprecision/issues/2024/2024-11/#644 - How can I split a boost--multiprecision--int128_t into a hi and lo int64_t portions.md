# #644 - How can I split a boost::multiprecision::int128_t into a hi and lo int64_t portions? [Closed]

> Username: CatrielCarbonera  
> Created at: 2024-11-24 02:01:17 UTC  
> Updated at: 2024-11-25 19:20:09 UTC  
> Closed at: 2024-11-25 19:19:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/644  

Two 64-bit integers, i0 and i1, are being compressed into a single 128-bit integer using the formula i = i0 * 2^64 + i1. The resulting multi-precision integer will undergo various arithmetic operations. However, I'm struggling to define the reverse operation to extract the original i0 and i1 values from the multi-precision integer.  
  
The goal is to create the function `unpack `that is inverse of the function `pack `is defined by  
```  
using int128_t = boost::multiprecision::int128_t;  
  
auto pack = [](const std::array<int64_t, 2>& value) {  
    return (((static_cast<int128_t>(1) << 64) * value[0]) + value[1]);  
};  
```  
The following code fails to properly invert the packing with boost::multiprecision::int128_t : C++  
```  
  
auto unpack = [](const int128_t& value) {  
    const auto lo = static_cast<int64_t>(value % (static_cast<int128_t>(1) << 64));  
    return std::array<int64_t, 2>{  
        static_cast<int64_t>((value - lo) / (static_cast<int128_t>(1) << 64)),  
        lo  
    };  
};  
  
```  
The unpacking function doesn't work as expected. However, the same code works fine when using __int128 (a two's complement implementation). Does anyone know how to implement the unpack function to work correctly with Boost Multiprecision?  
  
I've tried packing and unpacking uint64_t pairs, which works well. Unfortunately, the resulting extended-precision integers differ depending on whether the high-low values are represented as int64_t or uint64_t pairs. This [link](https://coliru.stacked-crooked.com/a/668b926f7f98e32e) points to an example that illustrates the problem.  
  
A version of the code that tests both boost's and native's int128 implementations is available by following the [link](https://coliru.stacked-crooked.com/a/8f337036724fcb5d):  
  
```  
#include <iostream>  
#include <algorithm>  
#include <vector>  
#include <cmath>  
#include <array>  
#include <iomanip>  
#include <utility>  
#include <algorithm>  
#include "boost/multiprecision/cpp_int.hpp"  
  
#pragma GCC diagnostic ignored "-Wpedantic"  
  
  
template <typename OStream> OStream& operator<< (OStream& stream, const std::array<int64_t, 2>& value)  
{  
    stream << "{ " << value[0] << ", " << value[1] << " }";  
      
    return stream;  
}  
  
template <typename OStream> OStream& operator<< (OStream& stream, __int128 value)  
{  
    if (value == 0) {  
        stream << "0";  
        return stream;  
    }  
  
    std::string result;  
    const auto is_negative = (value < 0);  
  
    if (is_negative)   
        value = -value;  
  
    while (value > 0) {  
        result = char('0' + (value % 10)) + result;  
        value /= 10;  
    }  
  
    if (is_negative)  
        result = "-" + result;  
  
    stream << result;  
  
    return stream;  
}  
  
int main()  
{  
#define USE_INT128  
#ifdef USE_INT128  
    using int128_t = __int128;  
    constexpr auto ExtendedType = " Extended type __int128";  
#else  
    using int128_t = boost::multiprecision::int128_t;  
    constexpr auto ExtendedType = " Extended type boost::multiprecision::int128_t";  
#endif  
  
    auto pack = [](const std::array<int64_t, 2>& value) {  
        return (((static_cast<int128_t>(1) << 64) * value[0]) + value[1]);  
    };  
      
    auto unpack = [](const int128_t& value) {  
        const auto lo = static_cast<int64_t>(value % (static_cast<int128_t>(1) << 64));  
        return std::array<int64_t, 2>{  
            static_cast<int64_t>((value - lo) / (static_cast<int128_t>(1) << 64)),  
            lo  
        };  
    };  
  
    for (auto hiLo : {   
        std::array<int64_t, 2> { static_cast<int64_t> (-2), (static_cast<int64_t> (1) << 63) },  
        std::array<int64_t, 2> { static_cast<int64_t> (-1), 1 },  
        std::array<int64_t, 2> { 1, -10 },   
        std::array<int64_t, 2> { 1, (static_cast<int64_t> (1) << 63) }   
        })  
    {  
        std::cout << "====================================" << ExtendedType << std::endl;  
        std::cout << "hiLo    = " << hiLo << std::endl;  
        const auto extended = pack (hiLo);  
        const auto backToHiLo = unpack (extended);  
  
        std::cout << "extended = " << extended << std::endl;  
        if (hiLo != backToHiLo)  
         std::cout << "Failed to unpack - to_HiLo (to_ExtendedInteger (" << hiLo << ")) = " << backToHiLo << std::endl;  
        std::cout << "eExtended   = " << pack (backToHiLo) << std::endl << std::endl;  
     }  
  
    return 0;  
}  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-11-24 09:48:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/644#issuecomment-2495900846  

Strictly speaking what you're doing (even with native 128-bit integers) invokes undefined behaviour.  So I would:  
  
* Take the abs value and cast to a uint128_t.  
* Unpack that to 2 uint64_t's.  
* Cast to int64_t's and adjust signs as required.  
  
Even then you're still not out of the woods as the low 64 bit part may end up negative in the result if bit 64 is set.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2024-11-24 10:19:55 UTC  
> Updated at: 2024-11-24 10:20:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/644#issuecomment-2495921663  

> Strictly speaking what you're doing (even with native 128-bit integers) invokes undefined behaviour.  
  
Because shifting is not _always_ the same as dividing, I believe. Also are you sure you want to be multiplying by $2^n$? Maybe it might be simpler to simply left/right shift? That is once, as @jzmaddock suggests, you cast to unsigned.

---

## Comment 3

> Username: CatrielCarbonera  
> Created at: 2024-11-24 14:05:09 UTC  
> Updated at: 2024-11-24 14:26:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/644#issuecomment-2496021219  

> > Strictly speaking what you're doing (even with native 128-bit integers) invokes undefined behaviour.  
>   
> Because shifting is not _always_ the same as dividing, I believe. Also are you sure you want to be multiplying by 2 n ? Maybe it might be simpler to simply left/right shift? That is once, as [@jzmaddock](https://github.com/jzmaddock) suggests, you cast to unsigned.  
  
Where is shifting being performed to divide? I am indeed, as you pointed out, using the shift operator to create the number 2^n. I have tried everything @jzmaddock suggested but failed to get the correct results. Could you please provide a snippet of code that will do what I need?

---

## Comment 4

> Username: CatrielCarbonera  
> Created at: 2024-11-24 14:12:40 UTC  
> Updated at: 2024-11-24 15:56:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/644#issuecomment-2496025483  

> Strictly speaking what you're doing (even with native 128-bit integers) invokes undefined behaviour. So I would:  
>   
> * Take the abs value and cast to a uint128_t.  
> * Unpack that to 2 uint64_t's.  
> * Cast to int64_t's and adjust signs as required.  
>   
> Even then you're still not out of the woods as the low 64 bit part may end up negative in the result if bit 64 is set.  
  
I am packing 32-bit integers. Not 64 bit integers. Could you please tell me what portion of what's being done by the pack and unpack functions is undefined? There's not a single integer operation in that program that overflows.  
  
I updated the text of the issue to reflect this fact. There's never any opportunity for overflows. Can you please provide the snippet of code that will do what I need to do?  
  
 I'm interested in a solution that doesn't depend on intimate knowledge of your implementation. For example, the unpack function uses only knowledge of the __int128 being a two's complement implementation like every other int. If you could provide such a solution, I would greatly appreciate it. Or simply confirm that it's not possible and I will be on my merry way.  
  
I can correctly unpack correctly the `boost::multiprecision::int128_t` provided that I cast to `__int128` before unpacking.  Is the only way of unpacking that data correctly to reproduce the functionality needed from the `__int128` in this snippet of code?  
  
```  
    auto unpack = [](BigInt value) {  
            constexpr auto TwoPowerNBitsTemp = (__int128 (1) << 64);  
            __int128 tempValue = static_cast<__int128> (value);  
            const auto lo = static_cast<Int>(tempValue % TwoPowerNBitsTemp);  
            return std::array<Int, 2>{  
                static_cast<Int>((tempValue - lo) / TwoPowerNBitsTemp),  
                lo  
            };            
    };  
  
```  
This option does not work on Visual Studio. I would have to develop the equivalent functionality. Again, your help will be greatly appreciated.

---

## Comment 5

> Username: CatrielCarbonera  
> Created at: 2024-11-24 18:54:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/644#issuecomment-2496170851  

I managed to obtain a version of unpacking that works with my given set of examples, but am not confident that this will address all the cases. I had to read the implementation of the multi precision and, after much reading came up with this  
  
```  
inline std::array<int64_t, 2> unpack_int128_t (const boost::multiprecision::int128_t& value)   
{  
    constexpr auto TwoPowerNBits = boost::multiprecision::int128_t (1) << 64;  
    const auto lo = (value % TwoPowerNBits);  
    const auto hi = (value - static_cast<int64_t> (lo)) / TwoPowerNBits;  
      
    if (lo != (static_cast<int64_t> (lo)))  
    {  
        return ((lo > 0) ?  
            std::array { static_cast<int64_t>(hi + 1), static_cast<int64_t> (lo - TwoPowerNBits) } :  
            std::array { static_cast<int64_t>(hi - 1), static_cast<int64_t> (lo + TwoPowerNBits) });  
    }  
  
    return std::array { static_cast<int64_t>(hi), static_cast<int64_t> (lo) };  
}  
  
```  
  
I am still not confident that this will indeed solve my problem, but seems to work with the few cases that I have tried. It would be helpful if the library  would provide a function that would split a multiprecision object  n-integers that satisfy:  
$a = \sum_{k = 0}^{N} a_i * 2^k$  
The function could work just like convert_to<...>, but in this case a vector with the minimal set of integers is returned. Seems to me that the implementation almost has that in the limbs, but that might need some work to support signed integers.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2024-11-25 09:17:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/644#issuecomment-2497369595  

>I am still not confident that this will indeed solve my problem, but seems to work with the few cases that I have tried. It would be >helpful if the library would provide a function that would split a multiprecision object n-integers that satisfy:  
>a = ∑ k = 0 N a i ∗ 2 k   
  
The problem is that signed integers do not satisfy that relation, so for example given:  
  
int64_t low, high;  
  
What value do they represent?  
  
Before you say 2^64*high + low remember that would mean the values in the half open range [2^63, 2^64) have no representation in that format.

---

## Comment 7

> Username: CatrielCarbonera  
> Created at: 2024-11-25 15:27:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/644#issuecomment-2498331281  

> > I am still not confident that this will indeed solve my problem, but seems to work with the few cases that I have tried. It would be >helpful if the library would provide a function that would split a multiprecision object n-integers that satisfy:  
> > a = ∑ k = 0 N a i ∗ 2 k  
>   
> The problem is that signed integers do not satisfy that relation, so for example given:  
>   
> int64_t low, high;  
>   
> What value do they represent?  
>   
> Before you say 2^64*high + low remember that would mean the values in the half open range [2^63, 2^64) have no representation in that format.  
  
@jzmaddock, thank you for your reply.  You raise a very valid point. In my haste,  I left out some important details. Please, allow me to clarify my request.  Let, $a$, and $a_i$ for $i=0, ..., N-1$, satisfy  
  
1. $a = \sum_{k =0}^{N-1} a_i * 2^ {i * M}$   
2. $a_i$ is such that $|a_i| < (2^{M-1} - 1) $,  $i = 0, ..., N-1$   
  
It follows that $|a| < 2^{N * M}-1$ from (1), (2).  
  
This equation has a solution for two's complement $(N * M)$-bit signed integer representation of $a$ where each $a_i$ is represented using a two's-complement $M$-bit representation.    
  
My question is: Will my proposed solution, defined by the `unpack_int128_t` function below, work with your implementation (presumably using one's complement) when $M=64, N=24,$ and $|a_i| < (2^{63} - 1)$, for $i=0, 1$?  
  
  
```  
inline std::array<int64_t, 2> unpack_int128_t (const boost::multiprecision::int128_t& value)   
{  
    constexpr auto TwoPowerNBits = boost::multiprecision::int128_t (1) << 64;  
    const auto lo = (value % TwoPowerNBits);  
    const auto hi = (value - static_cast<int64_t> (lo)) / TwoPowerNBits;  
      
    if (lo != (static_cast<int64_t> (lo)))  
    {  
        return ((lo > 0) ?  
            std::array { static_cast<int64_t>(hi + 1), static_cast<int64_t> (lo - TwoPowerNBits) } :  
            std::array { static_cast<int64_t>(hi - 1), static_cast<int64_t> (lo + TwoPowerNBits) });  
    }  
  
    return std::array { static_cast<int64_t>(hi), static_cast<int64_t> (lo) };  
}  
  
```  
  
I'm looking forward to your validation, which will close this issue for me.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2024-11-25 17:48:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/644#issuecomment-2498668036  

I *think* so, but I wouldn't like to swear to it :(  
  
I came up with:  
  
```  
std::array<int64_t, 2> unpack_int128_t(const boost::multiprecision::int128_t& value)  
{  
   constexpr auto TwoPowerNBits = boost::multiprecision::int128_t(1) << 64;  
  
   boost::multiprecision::int128_t high, low;  
  
   boost::multiprecision::divide_qr(value, TwoPowerNBits, high, low);  
  
   if (boost::multiprecision::bit_test(value, 63))  
   {  
      low -= TwoPowerNBits;  
      ++high;  
   }  
  
   std::array<int64_t, 2> result = { static_cast<int64_t>(high), static_cast<int64_t>(low) };  
  
   assert(boost::multiprecision::int128_t(result[0]) * TwoPowerNBits + result[1] == value);  
  
   return result;  
}  
```  
  
Which I *hope* is a little easier to see what's going on.  
  
It would be nice to avoid `divide_qr` which is more efficient than separate / and %, but still horribly inefficient compared to bit-fiddling, but I don't see a good bit-fiddling solution at present!

---

## Comment 9

> Username: CatrielCarbonera  
> Created at: 2024-11-25 19:20:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/644#issuecomment-2498845794  

@jzmaddock , thanks for your reply!
