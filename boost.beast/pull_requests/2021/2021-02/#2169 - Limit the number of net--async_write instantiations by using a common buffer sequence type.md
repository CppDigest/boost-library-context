# #2169 Limit the number of net::async_write instantiations by using a common buffer sequence type. [Closed]

> Username: chriskohlhoff  
> Created at: 2021-02-23 09:17:41 UTC  
> Updated at: 2021-03-01 21:40:17 UTC  
> Closed at: 2021-03-01 21:40:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2169  

Reduces the size of generated code for websockets. E.g. on macOS for websocket/client/async example:  
  
before:  
```  
__TEXT	__DATA	__OBJC	others	dec	hex  
933888	32768	0	4296146944	4297113600	10020c000  
```  
  
after:  
```  
__TEXT	__DATA	__OBJC	others	dec	hex  
884736	32768	0	4296048640	4296966144	1001e8000  
```

---
