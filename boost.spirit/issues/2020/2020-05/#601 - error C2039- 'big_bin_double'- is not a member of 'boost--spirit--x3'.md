# #601 - error C2039: 'big_bin_double': is not a member of 'boost::spirit::x3' [Closed]

> Username: rockonedege  
> Created at: 2020-05-25 17:18:17 UTC  
> Updated at: 2020-05-26 12:50:29 UTC  
> Closed at: 2020-05-25 22:22:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/601  

I am implements a protocol that sends/receives float/double across the network.  
  
I could use `karma::big_bin_double`, `karma::big_bin_double' to convert  them to binary representation.  
  
However, it generates errors to parse them from binary. Checking the source code I found this snippet But I don't know what the comment is about and how to get around make it work.  
  
```cpp  
    // Use a pseudo configuration macro to make clear that endian library support  
    // for floating point types is required. Must be removed as soon as the endian library  
    // properly supports floating point types.  
#ifdef BOOST_ENDIAN_HAS_FLOATING_POINT  
    BOOST_SPIRIT_MAKE_BINARY_PRIMITIVE(bin_float, native, float, 32)  
    BOOST_SPIRIT_MAKE_BINARY_PRIMITIVE(big_bin_float, big, float, 32)  
    BOOST_SPIRIT_MAKE_BINARY_PRIMITIVE(little_bin_float, little, float, 32)  
    BOOST_SPIRIT_MAKE_BINARY_PRIMITIVE(bin_double, native, double, 64)  
    BOOST_SPIRIT_MAKE_BINARY_PRIMITIVE(big_bin_double, big, double, 64)  
    BOOST_SPIRIT_MAKE_BINARY_PRIMITIVE(little_bin_double, little, double, 64)  
#endif  
```

---

## Comment 1

> Username: rockonedege  
> Created at: 2020-05-25 17:20:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/601#issuecomment-633659135  

and I am curious why it's available in `qi` and `karma`, but optional in `x3`?

---

## Comment 2

> Username: Kojoley  
> Created at: 2020-05-25 19:01:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/601#issuecomment-633686130  

The comment in the code you have quoted is pretty clear about why, and there is already #338 about the issue. I just opened #602 with a fix, you could try it if you are interested.

---

## Comment 3

> Username: rockonedege  
> Created at: 2020-05-26 02:36:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/601#issuecomment-633777364  

I copied the latest commit to have replaced the copy shipped with boost 1.73. But I encountered this error:  
```cpp  
F:\vcpkg\installed\x64-windows\include\boost/spirit/home/support/detail/hold_any.hpp(53,9): error C3646: 'BOOST_OVERRIDE': unknown override specifier   
F:\vcpkg\installed\x64-windows\include\boost/spirit/home/karma/stream/stream.hpp(127,1): error C3646: 'BOOST_OVERRIDE': unknown override specifier  
```  
Having searched the whole `boost` source tree. It seems to be only used by spirit, but nowhere defined.

---

## Comment 4

> Username: Kojoley  
> Created at: 2020-05-26 12:50:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/601#issuecomment-634004204  

That macro is from bleeding edge Boost.Config
