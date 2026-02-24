# #64 - Websocket handshake will not use the given resource path [Closed]

> Username: salutant  
> Created at: 2016-08-26 08:33:51 UTC  
> Updated at: 2016-08-26 17:43:09 UTC  
> Closed at: 2016-08-26 17:43:09 UTC  
> Url: https://github.com/boostorg/beast/issues/64  

It will use a regular path '/'.  
Here is the code in stream.ipp .  
  
``` cpp  
template<class NextLayer>  
http::request_v1<http::empty_body>  
stream<NextLayer>::  
build_request(boost::string_ref const& host,  
    boost::string_ref const& resource, std::string& key)  
{  
    http::request_v1<http::empty_body> req;  
    req.url = "/";  
    req.version = 11;  
    req.method = "GET";  
    req.headers.insert("Host", host);  
    req.headers.insert("Upgrade", "websocket");  
    key = detail::make_sec_ws_key(maskgen_);  
    req.headers.insert("Sec-WebSocket-Key", key);  
    req.headers.insert("Sec-WebSocket-Version", "13");  
    (*d_)(req);  
    http::prepare(req, http::connection::upgrade);  
    return req;  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-08-26 10:33:25 UTC  
> Url: https://github.com/boostorg/beast/issues/64#issuecomment-242695424  

Yeah that's a bug for sure!
