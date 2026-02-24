# #194 - Configurable maximum header size. [Closed]

> Username: avalchev  
> Created at: 2016-11-18 18:07:40 UTC  
> Updated at: 2016-11-18 20:10:04 UTC  
> Closed at: 2016-11-18 20:10:04 UTC  
> Url: https://github.com/boostorg/beast/issues/194  

I'm currently sending HTTP requests to Beast server with header value of 20000 bytes. It behaves really well by rejecting client request (closing connection).  
  
However, is it possible to configure maximum header size ?  
  
As far as I remember Joyent http parser, doesn't have such option and maximum size is fixed to 8086 (or something...).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-11-18 18:08:49 UTC  
> Updated at: 2016-11-18 18:09:20 UTC  
> Url: https://github.com/boostorg/beast/issues/194#issuecomment-261600198  

Yes, see:  
http://vinniefalco.github.io/beast/beast/ref/http__header_max_size.html  
  
In order to make use of this option, you have to call `async_parse` instead of `async_read`. And you need to set the options on the parser, and manage the parser's lifetime.

---

## Comment 2

> Username: avalchev  
> Created at: 2016-11-18 18:12:06 UTC  
> Updated at: 2016-11-18 18:14:30 UTC  
> Url: https://github.com/boostorg/beast/issues/194#issuecomment-261600975  

Something is not clear for me.   
  
I'm reading response with:  
  
```  
beast::http::async_read(_socket, request, action);  
```  
  
How to set this option, using `boost::http::prepare(request, option)` ?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-11-18 18:37:31 UTC  
> Url: https://github.com/boostorg/beast/issues/194#issuecomment-261607146  

> In order to make use of this option, you have to call async_parse instead of async_read. And you need to set the options on the parser, and manage the parser's lifetime.  
  
Something like this:  
  
```  
template<class AsyncReadStream>  
void do_read(AsyncReadStream& stream, yield_context yield)  
{  
    parser_v1<true, string_body, fields> p;  
    p.set_option(header_max_size{32768});  
    streambuf sb;  
    async_parse(stream, p, sb, yield);  
    message<true, string_body, fields> msg = p.release();  
}  
```

---

## Comment 4

> Username: avalchev  
> Created at: 2016-11-18 20:10:04 UTC  
> Url: https://github.com/boostorg/beast/issues/194#issuecomment-261629818  

Great example !  
  
I'll close the issue and reopen if need more help :)  
  
Thanks.
