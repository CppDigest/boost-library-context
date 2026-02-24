# #3 - GCC 4.9 conversion warning in types.h [Closed]

> Username: ghost  
> Created at: 2014-06-23 12:59:13 UTC  
> Updated at: 2014-12-10 20:16:46 UTC  
> Closed at: 2014-12-10 20:16:46 UTC  
> Url: https://github.com/boostorg/endian/issues/3  

Please excuse the paths; this is embedded in our sources until this goes into boost proper.  It was embedded verbatim with a tweak to the include guards.  
  
```  
/home/rleigh/code/bioformats/cpp/lib/ome/compat/endian/types.hpp:209:64: warning: conversion to ‘short unsigned int’ from ‘int’ may alter its value [-Wconversion]  
```  
  
```  
    template <typename T, std::size_t n_bytes,  
      bool sign=boost::is_signed<T>::value >  
    struct unrolled_byte_loops  
    {  
      typedef unrolled_byte_loops<T, n_bytes - 1, sign> next;  
  
      static T load_big(const unsigned char* bytes) BOOST_NOEXCEPT  
        { return *(bytes - 1) | (next::load_big(bytes - 1) << 8); }  
                                                               ^ here  
```  
  
This is when comparing endian types inside a boost.variant, but this is likely an unimportant detail.  It looks like GCC 4.9 with -Wconversion triggers this warning.  
  
It looks like the load_ and store_ functions here might be operating on types other than intended.  For example maybe this needs to use temporaries of defined type for the bitwise operations and/or a cast to the return type.  
  
Kind regards,  
Roger

---

## Comment 1

> Username: Beman  
> Created at: 2014-12-10 20:16:46 UTC  
> Url: https://github.com/boostorg/endian/issues/3#issuecomment-66516460  

These errors have been eliminated on the feature/buffer-types branch which will be merged to develop in the next week or so. The specific fix applied was to do a static_cast to the desired type. This was needed when type promotion occurred in an expression, IIUC.  
  
There is still one line in endian_operations_test.cpp that is causing a conversion warning. I need to analyze this case carefully as it may indicate I'm testing a combination of types that don't make sense.  
  
Thanks for the report,  
  
--Beman
