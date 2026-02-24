# #147 - bit_cast/padding_cast [Open]

> Username: gpeterhoff  
> Created at: 2023-05-31 19:08:15 UTC  
> Updated at: 2023-06-05 15:20:11 UTC  
> Url: https://github.com/boostorg/core/issues/147  

*Problem  
In many cases a bit_cast is needed. However, it cannot be assumed that std::bit_cast is available. Therefore it would make sense to "rebuild" this in boost; my implementation:  
[cast.hpp.txt](https://github.com/boostorg/core/files/11617919/cast.hpp.txt)  
  
*Functionality  
- if std::bit_cast or __builtin_bit_cast are available they are used -> constexpr  
- otherwise reinterpret_cast is used, but with the restrictions for std::bit_cast -> not constexpr  
  
*padding_cast  
- allows resizing  
  
thx  
Gero

---

## Comment 1

> Username: pdimov  
> Created at: 2023-05-31 19:11:42 UTC  
> Url: https://github.com/boostorg/core/issues/147#issuecomment-1570784932  

The current implementation is in `boost/core/bit.hpp`.

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2023-05-31 19:32:20 UTC  
> Url: https://github.com/boostorg/core/issues/147#issuecomment-1570822161  

But the implementation for bit_cast is not constexpr if std::bit_cast/__builtin_bit_cast are available. I'm sure you can easily adapt that - then I would be happy already :-)  
But I need my padding_cast.  
  
thx  
Gero

---

## Comment 3

> Username: Lastique  
> Created at: 2023-05-31 23:05:42 UTC  
> Url: https://github.com/boostorg/core/issues/147#issuecomment-1571078046  

It is not obvious as to what the behavior should be when the size of the source and target types are different. And such a behavior would likely be endian-dependent. I'm feeling skeptical about adding `padding_cast`, unless there is a strong case for one particular implementation and not any other.

---

## Comment 4

> Username: pdimov  
> Created at: 2023-05-31 23:08:23 UTC  
> Url: https://github.com/boostorg/core/issues/147#issuecomment-1571080129  

I don't think `padding_cast`, however specified, belongs here.  
  
I'll make use of `__builtin_bit_cast` for `constexpr` reasons though, because I've already done some `constexpr` work on the rest of `bit.hpp`.

---

## Comment 5

> Username: pdimov  
> Created at: 2023-05-31 23:10:58 UTC  
> Url: https://github.com/boostorg/core/issues/147#issuecomment-1571081864  

What is the intended use of `padding_cast`?

---

## Comment 6

> Username: gpeterhoff  
> Created at: 2023-06-01 00:54:40 UTC  
> Url: https://github.com/boostorg/core/issues/147#issuecomment-1571154658  

I need this for some math/FP functions, especially for the type boost::float80_t. The memory size of this type (sizeof) can be 16, 12 or 10 bytes depending on the platform/compiler/etc (of course only 10 bytes are relevant). To cast boost::float80_t e.g. to __int128 and back padding_cast is helpful.

---

## Comment 7

> Username: gpeterhoff  
> Created at: 2023-06-05 13:55:29 UTC  
> Url: https://github.com/boostorg/core/issues/147#issuecomment-1576853291  

Hello Peter,  
I have seen that you have customized bit_cast (bit.hpp). But this is incomplete:  
  
constexpr case  
You only check if __builtin_bit_cast is available. But it cannot be assumed that all compilers support this. Therefore you have to check additionally for std::bit_cast (__cpp_lib_bit_cast >= 201806L).  
  
non constexpr case  
You only check the size (BOOST_STATIC_ASSERT(sizeof(To)==sizeof(From)), but bit_cast is subject to further restrictions: https://en.cppreference.com/w/cpp/numeric/bit_cast  
  
thx & regards  
Gero

---

## Comment 8

> Username: pdimov  
> Created at: 2023-06-05 14:59:58 UTC  
> Url: https://github.com/boostorg/core/issues/147#issuecomment-1576964946  

At the moment all the compilers that have a `constexpr` `std::bit_cast` implement it via the intrinsic.  
  
Checking the further restrictions requires type traits, but Core can't use TypeTraits (or `<type_traits>` because we still support C++03.)

---

## Comment 9

> Username: gpeterhoff  
> Created at: 2023-06-05 15:20:11 UTC  
> Url: https://github.com/boostorg/core/issues/147#issuecomment-1577007480  

You can use boost::type_traits ?
