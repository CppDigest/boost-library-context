# #660 - set_params, set_encoded_params [Closed]

> Username: vinniefalco  
> Created at: 2023-01-13 15:17:47 UTC  
> Updated at: 2023-01-20 02:50:34 UTC  
> Closed at: 2023-01-20 02:50:34 UTC  
> Url: https://github.com/boostorg/url/issues/660  

Just an idea, not sure how badly we need it but it is pretty trivial:  
  
```  
url_base&  
url_base::  
set_params( std::initializer_list< param_view> );  
  
url_base&  
url_base::  
set_encoded_params( std::initializer_list< param_pct_view > );  
```
