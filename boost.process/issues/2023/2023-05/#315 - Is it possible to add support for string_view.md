# #315 - Is it possible to add support for string_view? [Open]

> Username: VitorRamos  
> Created at: 2023-05-26 14:11:07 UTC  
> Updated at: 2023-06-18 15:46:31 UTC  
> Url: https://github.com/boostorg/process/issues/315  

Example:  
  
https://github.com/boostorg/process/blob/3211afda4acc453c426d07673b43d1b2bd78d30d/include/boost/process/environment.hpp#LL260C51-L260C51  
  
```c++  
    iterator        find( const string_type& key )  
```  
  
Adding template argument to those methods, something like this:  
```c++  
    template<typename String>  
    iterator        find( const String& key )  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-06-13 13:05:12 UTC  
> Url: https://github.com/boostorg/process/issues/315#issuecomment-1589274996  

Do you want std::string_view specifically or any string_view ?

---

## Comment 2

> Username: VitorRamos  
> Created at: 2023-06-18 15:46:31 UTC  
> Url: https://github.com/boostorg/process/issues/315#issuecomment-1596187852  

I think any would be better
