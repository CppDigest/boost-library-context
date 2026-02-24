# #146 - Add remove_member_pointer [Open]

> Username: pdimov  
> Created at: 2020-09-12 15:55:24 UTC  
> Updated at: 2020-09-12 15:55:24 UTC  
> Url: https://github.com/boostorg/type_traits/issues/146  

```  
template<class Pm> struct remove_member_pointer  
{  
    typedef Pm type;  
};  
  
template<class T, class C> struct remove_member_pointer<T C::*>  
{  
    typedef T type;  
};  
```
