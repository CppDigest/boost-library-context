# #1656 - How to clear recveive buffer after read when buffer is reused. [Open]

> Username: smartseal  
> Created at: 2019-07-15 12:40:43 UTC  
> Updated at: 2019-07-16 03:45:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1656  

I am using REST protocol with BEAST https. I try to reuse the beast::buffer::flat_buffer.  
  
But I found each time I send a request and read from http::response<http::string_body>. the buffer didn't clear the result from previous request.   
For example, first time the server send back "[]", the second time I read is "[][next time result]".  
  
I called response->clear() and buff->consume(0); it does'nt work.  
  
How to clear the buffer after I have processed the result and prepare the buffer for next time read?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-07-15 14:36:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1656#issuecomment-511429117  

```  
*msg = {};  
```  
  
See:  
https://github.com/boostorg/beast/blob/develop/example/http/server/async/http_server_async.cpp#L284

---

## Comment 2

> Username: smartseal  
> Created at: 2019-07-16 01:56:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1656#issuecomment-511633596  

I call *response = {}, and it works. Thanks.  
  
I called response->clear() and mbuf->consume() but it doesn't work.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-07-16 03:25:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1656#issuecomment-511649806  

This needs to be redesigned to be more obvious
