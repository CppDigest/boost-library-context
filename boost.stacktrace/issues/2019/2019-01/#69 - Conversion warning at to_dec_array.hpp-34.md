# #69 - Conversion warning at to_dec_array.hpp:34 [Closed]

> Username: Caian  
> Created at: 2019-01-02 12:34:05 UTC  
> Updated at: 2019-01-05 10:49:18 UTC  
> Closed at: 2019-01-05 10:48:54 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/69  

I am receiving the following warning when compiling with `-Wconversion`:  
  
```  
boost/stacktrace/detail/to_dec_array.hpp: In function ‘boost::array<char, 40ul> boost::stacktrace::detail::to_dec_array(std::size_t)’:  
boost/stacktrace/detail/to_dec_array.hpp:34:31: warning: conversion to ‘char’ from ‘std::size_t {aka long unsigned int}’ may alter its value [-Wconversion]  
         ret[digits - i] = '0' + (value % 10);  
```  
  
Although it is not an error, I think a `static_cast` can be used to avoid unnecessary compiler output:  
  
```  
ret[digits - i] = static_cast<char>('0' + (value % 10));  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-01-05 10:49:14 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/69#issuecomment-451645322  

Many thanks for the report!
