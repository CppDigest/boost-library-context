# #992 - Example http_server_async.cpp size() on moved away object? [Closed]

> Username: SimonEbner  
> Created at: 2018-01-20 21:29:16 UTC  
> Updated at: 2018-01-25 22:07:34 UTC  
> Closed at: 2018-01-25 22:07:34 UTC  
> Url: https://github.com/boostorg/beast/issues/992  

I was going through the examples and wondering if the line 197 in the example http_server_async.cpp is correct  
  
https://github.com/boostorg/beast/blob/2555942010351fa2cf17623bc5f7330c7681b180/example/http/server/async/http_server_async.cpp#L197  
  
since it is calling `size()` on the object that has been moved in line 193?  
https://github.com/boostorg/beast/blob/2555942010351fa2cf17623bc5f7330c7681b180/example/http/server/async/http_server_async.cpp#L193  
  
Or if it's working correctly if it's not brittle to say the least?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-20 21:30:48 UTC  
> Url: https://github.com/boostorg/beast/issues/992#issuecomment-359203659  

How the heck does that even work? Yeah it looks off...
