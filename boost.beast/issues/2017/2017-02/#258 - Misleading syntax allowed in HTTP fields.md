# #258 - Misleading syntax allowed in HTTP fields [Closed]

> Username: vinniefalco  
> Created at: 2017-02-07 19:56:34 UTC  
> Updated at: 2017-05-08 00:04:36 UTC  
> Closed at: 2017-05-08 00:04:36 UTC  
> Url: https://github.com/boostorg/beast/issues/258  

This assignment compiles but does nothing:  
```  
request<string_body> req;  
req.fields["User-Agent"] = "Beast";  
```
