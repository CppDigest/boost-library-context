# #338 - x3 floating point support in binary endianness parsers [Closed]

> Username: octopus-prime  
> Created at: 2017-12-20 14:38:36 UTC  
> Updated at: 2020-05-25 22:22:54 UTC  
> Closed at: 2020-05-25 22:22:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/338  

In Qi there was support for  
- bin_float  
- big_bin_float  
- little_bin_float  
- bin_double  
- big_bin_double  
- little_bin_double  
  
In X3 is not because of  
```  
    // Use a pseudo configuration macro to make clear that endian libray support  
    // for floating point types is required. Must be removed as soon as the endian library  
    // properly supports floating point types.  
```  
  
But waiting for the missing endian libray support is futile, because of  
```  
Why is there no floating point support?  
  
    An attempt was made to support four-byte floats and eight-byte doubles, limited to IEEE 754 (also know as ISO/IEC/IEEE 60559) floating point and further limited to systems where floating point endianness does not differ from integer endianness.  
  
    Even with those limitations, support for floating point types was not reliable and was removed. For example, simply reversing the endianness of a floating point number can result in a signaling-NAN. For all practical purposes, binary serialization and endianness for integers are one and the same problem. That is not true for floating point numbers, so binary serialization interfaces and formats for floating point does not fit well in an endian-based library.  
```  
  
So, if we want x3 floating point support in binary endianness parsers, we have to do it ourself - like in Qi.

---

## Comment 1

> Username: djowel  
> Created at: 2018-01-04 03:29:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/338#issuecomment-355190768  

👍
