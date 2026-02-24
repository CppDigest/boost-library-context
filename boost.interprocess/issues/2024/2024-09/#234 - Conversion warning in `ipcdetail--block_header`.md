# #234 - Conversion warning in `ipcdetail::block_header` [Closed]

> Username: k3DW  
> Created at: 2024-09-28 03:49:54 UTC  
> Updated at: 2024-09-29 15:05:40 UTC  
> Closed at: 2024-09-29 15:05:40 UTC  
> Url: https://github.com/boostorg/interprocess/issues/234  

This issue was seen in boostorg/unordered#287, with 2 different template parameters  
```  
./boost/interprocess/detail/segment_manager_helper.hpp:276:46: error: conversion to 'const unsigned int:30' from 'unsigned int' may alter its value [-Werror=conversion]  
       , m_value_bytes(val_bytes& (value_mask))  
```  
```  
./boost/interprocess/detail/segment_manager_helper.hpp:276:46: error: conversion to 'const long unsigned int:62' from 'long unsigned int' may alter its value [-Werror=conversion]  
       , m_value_bytes(val_bytes& (value_mask))  
```  
This is happening specifically with GCC 5. On my machine on GCC 5, writing `(~size_type(0) >> 2u)` in place of `(value_mask)` seems to fix the problem, but then the name is removed. I'm not sure the best course of action here.
