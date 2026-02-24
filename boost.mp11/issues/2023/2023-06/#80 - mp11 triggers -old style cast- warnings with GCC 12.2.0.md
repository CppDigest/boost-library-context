# #80 - mp11 triggers "old style cast" warnings with GCC 12.2.0 [Closed]

> Username: ilyapopov  
> Created at: 2023-06-23 14:00:27 UTC  
> Updated at: 2023-06-23 16:48:13 UTC  
> Closed at: 2023-06-23 16:48:13 UTC  
> Url: https://github.com/boostorg/mp11/issues/80  

When compiling code with `-Wall -Wextra`, gcc produces the following:  
  
```  
external/mp11/include/boost/mp11/detail/mp_map_find.hpp:77:47: warning: use of old-style cast to ‘using U = struct boost::mp11::mp_inherit<boost::mp11::mp_identity<T>...>*’ [-Wold-style-cast]  
   77 |     using type = mpmf_unwrap< decltype( f((U*)0) ) >;  
      |                                               ^  
external/mp11/include/boost/mp11/detail/mp_map_find.hpp:77:47: warning: use of old-style cast to ‘using U = struct boost::mp11::mp_inherit<boost::mp11::mp_identity<T>...>*’ [-Wold-style-cast]  
external/mp11/include/boost/mp11/algorithm.hpp:312:45: warning: use of old-style cast to ‘struct boost::mp11::mp_identity<T>*’ [-Wold-style-cast]  
  312 |     using R = decltype( f( (mp_identity<T>*)0 ... ) );  
      |                                             ^  
external/mp11/include/boost/mp11/algorithm.hpp:312:45: warning: use of old-style cast to ‘struct boost::mp11::mp_identity<T>*’ [-Wold-style-cast]  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2023-06-23 14:04:00 UTC  
> Url: https://github.com/boostorg/mp11/issues/80#issuecomment-1604332135  

Would you like to do a PR replacing these with `static_cast`?

---

## Comment 2

> Username: ilyapopov  
> Created at: 2023-06-23 14:06:51 UTC  
> Url: https://github.com/boostorg/mp11/issues/80#issuecomment-1604335900  

Yes, I will do this shortly.
