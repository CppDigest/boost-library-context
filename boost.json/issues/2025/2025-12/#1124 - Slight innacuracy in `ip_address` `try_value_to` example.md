# #1124 - Slight innacuracy in `ip_address` `try_value_to` example [Open]

> Username: pdimov  
> Created at: 2025-12-01 17:12:48 UTC  
> Updated at: 2025-12-01 17:12:48 UTC  
> Url: https://github.com/boostorg/json/issues/1124  

```  
    boost::system::result< unsigned char > oct1  
        = try_value_to< unsigned char >( arr[0] );  
    if( !oct1 )  
        return make_error_code( std::errc::invalid_argument );  
```  
This should be  
```  
    if( !oct1 )  
        return oct1.error();  
```  
so as to not discard the error returned from the nested `try_value_to` (which is going to be more informative and may include a source location).
