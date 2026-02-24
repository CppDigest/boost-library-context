# #2959 - Question: RFC 7235 authentication [Closed]

> Username: pfeatherstone  
> Created at: 2024-12-05 09:13:07 UTC  
> Updated at: 2025-04-15 13:19:19 UTC  
> Closed at: 2025-04-15 13:19:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2959  

I'm trying to implement basic authentication but struggling a bit.  
  
I'm implementing  
  
![Image](https://github.com/user-attachments/assets/276f752d-ec0e-45d7-9750-cdc44fd5574a)  
  
For the initial 401 response i'm using   
  
```  
using http_request  = boost::beast::http::request<boost::beast::http::string_body>;  
  
auto http_unauthorized (const http_request& req)  
{  
    http::response<http::empty_body> res{http::status::unauthorized, req.version()};  
    res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
    res.set(http::field::www_authenticate, "Basic realm: \"Access to the staging site\"");  
    res.keep_alive(req.keep_alive());  
    res.prepare_payload();  
    return res;  
}  
```  
  
The authorization request i get back from the browser is correct and I can decode the base64 encoded username and password. So that's all good.  
  
I then reply with 200 response using:  
  
```  
auto http_ok_status (const http_request& req)  
{  
    http::response<http::empty_body> res{http::status::ok, req.version()};  
    res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
    res.keep_alive(req.keep_alive());  
    res.prepare_payload();  
    return res;  
}  
```  
  
However what follows is one of two behaviours:   
* the browser doesn't resend the original request and the page doesn't load  
* or it does but uses multiple TCP connections for HTTP requests which all need to be authenticated again, so I'm having to input username and password for every javascript and CSS file it wants to load.  
  
Does that make sense? I'm forwarding the keep_alive requests everywhere trying to avoid multiple TCP connections.   
  
Has anybody got authentication working reliably with Beast ?

---

## Comment 1

> Username: pfeatherstone  
> Created at: 2024-12-05 09:16:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2959#issuecomment-2519712225  

Also I noticed that if i don't use `res.prepare_payload();` in `http_unauthorized()` the full response doesn't make its way to the browser correctly. Though in principle, I don't know why i need to call `prepare_payload()` on `http::empty_body` since by definition there is no payload...  
  
So do i have other similar bugs where full HTTP responses aren't making their way to the browser. Maybe there is chunked encoding somewhere. I don't know.

---

## Comment 2

> Username: ashtum  
> Created at: 2024-12-05 11:37:37 UTC  
> Updated at: 2025-04-15 13:12:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2959#issuecomment-2520072781  

> Also I noticed that if i don't use `res.prepare_payload();` in `http_unauthorized()` the full response doesn't make its way to the browser correctly. Though in principle, I don't know why i need to call `prepare_payload()` on `http::empty_body` since by definition there is no payload...  
>   
> So do i have other similar bugs where full HTTP responses aren't making their way to the browser. Maybe there is chunked encoding somewhere. I don't know.  
  
`401 Unauthorized` responses can include a body. If you omit the call to `prepare_payload`, the serialized message will not include a `Content-Length` header, causing the client to rely on an end-of-stream error to determine the end of the message.  
https://datatracker.ietf.org/doc/html/rfc2616#section-4.4  
>   1.Any response message which "MUST NOT" include a message-body (such  
     as the 1xx, 204, and 304 responses and any response to a HEAD  
     request) is always terminated by the first empty line after the  
     header fields, regardless of the entity-header fields present in  
     the message.

---

## Comment 3

> Username: ashtum  
> Created at: 2025-04-15 13:13:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2959#issuecomment-2805017582  

@pfeatherstone, has this resolved your issue?

---

## Comment 4

> Username: pfeatherstone  
> Created at: 2025-04-15 13:19:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2959#issuecomment-2805034579  

I did get this working but i can't remember what the fix was, it was a while ago. I've also since then written my own HTTP(s)+WS(s) library on top of Asio which i'm using in production and has authentication working. I have a repo but it's private for now. I need to write more examples then i'll make it public. Beast is quite bloated and takes ages to compile. That was the motivation. I have all the vocabulary types and composed operations working well. Anyway, i'll close for now.
