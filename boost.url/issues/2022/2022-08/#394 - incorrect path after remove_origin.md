# #394 - incorrect path after remove_origin [Closed]

> Username: vinniefalco  
> Created at: 2022-08-12 21:04:02 UTC  
> Updated at: 2022-10-03 20:49:30 UTC  
> Closed at: 2022-10-03 20:49:30 UTC  
> Url: https://github.com/boostorg/url/issues/394  

```  
url u( "http://www.example.com//kyle:xy" );  
u.remove_origin();  
assert( u.string() == "/.//kyle:xy" );  
```  
  
fails
