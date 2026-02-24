# #36 - floating point conversion [Closed]

> Username: jayghoshrao  
> Created at: 2019-10-16 13:46:19 UTC  
> Updated at: 2019-10-16 15:32:13 UTC  
> Closed at: 2019-10-16 15:32:13 UTC  
> Url: https://github.com/boostorg/endian/issues/36  

The 1.71 documentation is supposed to have floating point conversion. Yet I encounter BOOST_STATIC_ASSERT failures when I try to convert a double.   
  
I have boost version 1.71.0-2 loaded from the arch repositories.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-10-16 14:13:13 UTC  
> Url: https://github.com/boostorg/endian/issues/36#issuecomment-542721760  

Can you please provide a small program that demonstrates the problem?

---

## Comment 2

> Username: jayghoshrao  
> Created at: 2019-10-16 15:24:06 UTC  
> Url: https://github.com/boostorg/endian/issues/36#issuecomment-542755951  

[test.txt](https://github.com/boostorg/endian/files/3735080/test.txt)  
  
Here's a test case. The error is as follows.  
```  
  
In file included from /usr/include/boost/endian/detail/endian_reverse.hpp:13,  
                 from /usr/include/boost/endian/conversion.hpp:11,  
                 from test.cpp:2:  
/usr/include/boost/endian/conversion.hpp: In instantiation of ‘EndianReversible boost::endian::big_to_native(EndianReversible) [with EndianReversible = double]’:  
test.cpp:7:35:   required from here  
/usr/include/boost/endian/conversion.hpp:159:5: error: static assertion failed: detail::is_endian_reversible<EndianReversible>::value  
  159 |     BOOST_STATIC_ASSERT( detail::is_endian_reversible<EndianReversible>::value );  
      |     ^~~~~~~~~~~~~~~~~~~  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2019-10-16 15:32:13 UTC  
> Url: https://github.com/boostorg/endian/issues/36#issuecomment-542759893  

Floats aren't endian-reversible. If you reverse the bytes of a float, there's no guarantee that the result will be a valid float, or whether the bits will remain the same.  
  
You can use an `endian_buffer` or an `endian_arithmetic` with `float` or `double`, you can load or store floating point types into an array of `unsigned char` via `endian_load` and `endian_store`, but you can't use `endian_reverse` with a floating point type.
