# #270 - Return params_view from parse_query_params [Closed]

> Username: vinniefalco  
> Created at: 2022-07-28 21:56:34 UTC  
> Updated at: 2022-07-30 02:19:13 UTC  
> Closed at: 2022-07-30 02:19:13 UTC  
> Url: https://github.com/boostorg/url/issues/270  

Now that decoded ranges are free we should return the thing that users are most likely to want. And we should consider this signature:  
```  
auto parse_query_params(  
    optional< string_view > s) noexcept ->  
        result< params_encoded_view >;  
```

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-07-29 14:41:55 UTC  
> Url: https://github.com/boostorg/url/issues/270#issuecomment-1199427001  

Probably  
  
```cpp  
auto parse_query_params(  
    optional< string_view > s) noexcept ->  
        result< params_view >;  
```
