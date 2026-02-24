# #28 - GCC 8 warnings [Closed]

> Username: DesWurstes  
> Created at: 2018-06-11 17:17:19 UTC  
> Updated at: 2018-06-14 12:49:22 UTC  
> Closed at: 2018-06-14 12:49:22 UTC  
> Url: https://github.com/boostorg/signals2/issues/28  

```  
/usr/local/include/boost/signals2/last_value.hpp:54:36: warning: '*((void*)& value +1)' may be used uninitialized in this function [-Wmaybe-uninitialized]  
         if(value) return value.get();  
                                    ^  
[/usr/local/include/boost/signals2/last_value.hpp:43:21: note: '*((void*)& value +1)' was declared here  
         optional<T> value;  
                     ^~~~~  
```  
  
https://github.com/boostorg/signals2/blob/develop/include/boost/signals2/last_value.hpp#L54
