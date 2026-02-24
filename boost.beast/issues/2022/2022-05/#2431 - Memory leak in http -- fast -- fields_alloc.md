# #2431 - Memory leak in http -> fast -> fields_alloc [Closed]

> Username: deW1  
> Created at: 2022-05-22 00:47:26 UTC  
> Updated at: 2022-05-22 05:12:07 UTC  
> Closed at: 2022-05-22 05:12:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2431  

Allocation of buffer never gets released because ref count is decremented after the last reference check.  
  
Ex: https://godbolt.org/z/baGd7hvoE change line 56 `--refs`.  
  
The issue is at https://github.com/boostorg/beast/blob/develop/example/http/server/fast/fields_alloc.hpp#L59
