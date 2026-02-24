# #223 - Possible copypaste typo. [Closed]

> Username: pbalash0v  
> Created at: 2022-06-09 07:35:34 UTC  
> Updated at: 2022-06-25 17:54:43 UTC  
> Closed at: 2022-06-25 17:54:43 UTC  
> Url: https://github.com/boostorg/container/issues/223  

Hi,  
This looks like a copypaste typo:  
https://github.com/boostorg/container/blob/f548e87cf5d5197992dd25c48a47e1db4924cde6/include/boost/container/throw_exception.hpp#L100  
  
Presumably this should be:  
```c++  
typedef length_error length_error_t;  
```  
  
Regards,  
Pavel.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-06-25 17:54:43 UTC  
> Url: https://github.com/boostorg/container/issues/223#issuecomment-1166334231  

Thanks for the report!
