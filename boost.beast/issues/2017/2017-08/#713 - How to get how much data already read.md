# #713 - How to get how much data already read [Closed]

> Username: maytrue  
> Created at: 2017-08-02 16:03:08 UTC  
> Updated at: 2017-08-03 02:54:33 UTC  
> Closed at: 2017-08-03 02:54:33 UTC  
> Url: https://github.com/boostorg/beast/issues/713  

I send GET request with range   
  
> request.set(http::field::range, range)  
  
And I want to get how much data returns, so I try to parse response  
  
>   http::response_parser<http::string_body> parser;  
>     http::read(mSocket, readBuffer, parser, errorCode);  
  
And print content-length with zero  
  
> std::cout << *parser.content_length() << std::endl;   
  
But printing  parser.get()  got correct results.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-02 16:14:21 UTC  
> Url: https://github.com/boostorg/beast/issues/713#issuecomment-319721647  

`content_length` only reports the value of the Content-Length field if it is present. It will return `boost::none` if the Transfer-Encoding specifies `chunked` or if the HTTP response does not have the Content-Length field present (in this case, the end of the body is signaled by connection closure).  
  
Instead of calling `basic_parser::content_length` simply query the body container:  
  
```  
std::cout << parser.get().body.size() << std::endl;  
```  
  
Thanks

---

## Comment 2

> Username: maytrue  
> Created at: 2017-08-03 02:54:31 UTC  
> Url: https://github.com/boostorg/beast/issues/713#issuecomment-319854002  

OK , I got result from   
  
> std::cout << parser.get().payload_size()  
  
Thanks for replay
