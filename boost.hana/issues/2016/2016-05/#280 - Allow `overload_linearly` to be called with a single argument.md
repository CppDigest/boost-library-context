# #280 - Allow `overload_linearly` to be called with a single argument [Closed]

> Username: vittorioromeo  
> Created at: 2016-05-27 19:03:05 UTC  
> Updated at: 2016-05-29 17:10:19 UTC  
> Closed at: 2016-05-29 17:10:19 UTC  
> Url: https://github.com/boostorg/hana/issues/280  

Currently, [`overload_linearly`](http://www.boost.org/doc/libs/1_61_0/libs/hana/doc/html/group__group-functional.html#gaa46de6f618d9f14edb1589b36b6e75ec) requires at least two arguments. [`overload`](http://www.boost.org/doc/libs/1_61_0/libs/hana/doc/html/group__group-functional.html#ga83e71bae315e299f9f5f9de77b012139) can be successfully called with a single argument.  
  
Apart from the inconsistency between the two functions, it's also very useful to allow `overload_linear` to be called with a single argument in generic contexts where the user is passing a variadic number of functions:  
  
``` cpp  
template <typename... TFs>  
void called_by_user(TFs&&... fs)  
{  
    return hana::overload_linearly(some_wrapper(FWD(fs))...);  
}  
  
called_by_user([](auto){ }); // error!  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2016-05-29 17:10:19 UTC  
> Url: https://github.com/boostorg/hana/issues/280#issuecomment-222371454  

Fixed in #282
