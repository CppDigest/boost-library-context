# #379 - Simplify type traits [Closed]

> Username: glenfe  
> Created at: 2017-05-23 18:36:18 UTC  
> Updated at: 2017-05-24 21:35:14 UTC  
> Closed at: 2017-05-24 21:35:14 UTC  
> Url: https://github.com/boostorg/beast/issues/379  

For example, get_lowest_layer can lose around 20 lines of code, eliminating the need for has_lowest_layer, maybe_get_lowest_layer, by becoming:  
  
```cpp  
template<class T, class = void>  
struct get_lowest_layer  
{  
    using type = T;  
};  
  
template<class T>  
struct get_lowest_layer<T, void_t<typename T::lowest_layer_type> >  
{  
    using type = typename T::lowest_layer_type;  
};  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-23 19:38:14 UTC  
> Url: https://github.com/boostorg/beast/issues/379#issuecomment-303509107  

willfix
