# #264 - va_start() without va_end() [Open]

> Username: smuehlst  
> Created at: 2022-09-28 08:23:32 UTC  
> Updated at: 2023-07-10 04:38:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/264  

I stumbled over two places in the serialization code where `va_start()` is called without a corresponding call to `va_end()`:  
  
https://github.com/boostorg/serialization/blob/3f322d4adc3c88a667751ad66ce19217a3bba1f9/include/boost/serialization/extended_type_info_no_rtti.hpp#L130  
https://github.com/boostorg/serialization/blob/3f322d4adc3c88a667751ad66ce19217a3bba1f9/include/boost/serialization/extended_type_info_typeid.hpp#L116  
  
Is this intentional? According to my understanding of the C standard it is undefined behavior if `va_end()` is missing after `va_start()` has been called. See for example ISO/IEC 9899:TC3:  
  
> 7.15.1 Variable argument list access macros  
...  
Each invocation of the va_start and va_copy macros shall be matched by a corresponding invocation of the va_end macro in the same function.  
...  
7.15.1.3 The va_end macro  
...  
If there is no corresponding invocation of the va_start or va_copy macro, or if the va_end macro is not invoked before the return, the behavior is undefined.

---

## Comment 1

> Username: SilverPlate3  
> Created at: 2023-07-10 04:38:28 UTC  
> Url: https://github.com/boostorg/serialization/issues/264#issuecomment-1628151691  

@robertramey  
Would you like me to create a pull request and fix this issue?
