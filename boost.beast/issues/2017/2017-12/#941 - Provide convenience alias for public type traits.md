# #941 - Provide convenience alias for public type traits [Closed]

> Username: djarek  
> Created at: 2017-12-16 10:02:17 UTC  
> Updated at: 2018-02-22 00:54:29 UTC  
> Closed at: 2018-02-22 00:54:29 UTC  
> Url: https://github.com/boostorg/beast/issues/941  

A convenience alias should be provided for public traits, similarly to C++14's or ASIO's traits, example:  
```cpp  
template<typename T>  
using get_lowest_layer_t = typename get_lowest_layer<T>::type  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-16 13:15:21 UTC  
> Url: https://github.com/boostorg/beast/issues/941#issuecomment-352182847  

Which of the other public traits need a similar treatment?

---

## Comment 2

> Username: djarek  
> Created at: 2017-12-16 20:33:51 UTC  
> Url: https://github.com/boostorg/beast/issues/941#issuecomment-352209652  

Seems that `get_lowest_layer` is the only trait this is applicable to, because C++11 doesn't allow variable templates.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-12-17 00:52:05 UTC  
> Url: https://github.com/boostorg/beast/issues/941#issuecomment-352223114  

We should just change the API of `get_lowest_layer` to be the alias. This would be a breaking change. I doubt anyone will be using this obscure type.
