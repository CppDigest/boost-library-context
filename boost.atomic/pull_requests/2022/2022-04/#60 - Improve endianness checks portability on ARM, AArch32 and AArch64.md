# #60 Improve endianness checks portability on ARM, AArch32 and AArch64 [Merged]

> Username: Lastique  
> Created at: 2022-04-26 08:43:03 UTC  
> Updated at: 2022-04-27 15:41:34 UTC  
> Merged at: 2022-04-27 15:41:14 UTC  
> Closed at: 2022-04-27 15:41:14 UTC  
> Url: https://github.com/boostorg/atomic/pull/60  

When gcc predefined macros are not defined, use Boost.Predef (which also checks macros defined in the system endian.h header) to detect endianness. This may help with gcc impostor compilers that do not define built-in endianness macros.  
  
Closes https://github.com/boostorg/atomic/issues/59.

---
