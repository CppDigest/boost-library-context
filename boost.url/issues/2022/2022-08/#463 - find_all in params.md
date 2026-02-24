# #463 - find_all in params [Open]

> Username: vinniefalco  
> Created at: 2022-08-27 00:05:34 UTC  
> Updated at: 2022-09-02 17:22:33 UTC  
> Url: https://github.com/boostorg/url/issues/463  

we could use this signature in the params containers:  
```  
auto  
params::  
find_all( string_view key, ignore_case_param ic = {} ) const noexcept ->  
    subrange;  
```
