# #166 Avoid -Wconversion warning in is_complete [Closed]

> Username: pdimov  
> Created at: 2022-02-10 03:13:20 UTC  
> Updated at: 2022-02-10 13:11:06 UTC  
> Closed at: 2022-02-10 13:11:06 UTC  
> Url: https://github.com/boostorg/type_traits/pull/166  

This fixes the following warning:  
```  
./boost/type_traits/is_complete.hpp:47:14: error: conversion from 'long unsigned int' to 'unsigned int' may change value [-Werror=conversion]  
   47 |       ok_tag<sizeof(T)> check_is_complete(int);  
      |              ^~~~~~~~~  
```

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-02-10 03:15:40 UTC  
> Url: https://github.com/boostorg/type_traits/pull/166#issuecomment-1034449250  

Oh, that's a literal duplicate of https://github.com/boostorg/type_traits/pull/163. I didn't notice it. Feel free to merge either one. :-)

---
