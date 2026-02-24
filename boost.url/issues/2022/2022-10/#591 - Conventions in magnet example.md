# #591 - Conventions in magnet example [Closed]

> Username: alandefreitas  
> Created at: 2022-10-10 23:08:46 UTC  
> Updated at: 2022-10-17 21:32:18 UTC  
> Closed at: 2022-10-17 21:32:18 UTC  
> Url: https://github.com/boostorg/url/issues/591  

We want to return `pct_string_view` never `decode_view` unless the user asks for it.  
  
This doesn't need to be explicit  
  
```  
    explicit  
    is_url_with_key(  
        urls::string_view key,  
        std::string& buffer)  
```  
  
The javadoc comments are different from the library  
  
```  
/// Callable to convert param values to urls  
/**  
```  
  
The indentation and whitespace are different  
  
```  
struct param_view_to_infohash {  
    urls::string_view  
    operator()(urls::param_view p);  
};  
```
