# #2291 - Tooling for HTTP header lists parting [Closed]

> Username: Tectu  
> Created at: 2021-07-18 10:10:03 UTC  
> Updated at: 2021-07-18 15:05:39 UTC  
> Closed at: 2021-07-18 11:06:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2291  

Does `boost::beast` provide any tooling for parsing HTTP header lists? For example:  
```none  
Content-Type: multipart/form-data; boundary=something  
```  
How would one go about retrieving `multipart/form-data` and `boundary=something` as individual items? Does beast provide any tooling for that out of the box?  
  
While we're already at it: Does beast provide any tooling to parse the body of an `multipart/form-data` request? Example request (taken from MDN):  
```none  
POST /foo HTTP/1.1  
Content-Length: 68137  
Content-Type: multipart/form-data; boundary=---------------------------974767299852498929531610575  
  
-----------------------------974767299852498929531610575  
Content-Disposition: form-data; name="description"  
  
some text  
-----------------------------974767299852498929531610575  
Content-Disposition: form-data; name="myFile"; filename="foo.txt"  
Content-Type: text/plain  
  
(content of the uploaded file foo.txt)  
-----------------------------974767299852498929531610575--  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-07-18 10:37:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2291#issuecomment-882035555  

Beast is a low level HTTP/websocket library and as such does not offer tools for decoding cookies, mime formats and so on.   
One could envisage a higher level library, built on top of beast that did so.  
However, parsing mime documents is fairly trivial.

---

## Comment 2

> Username: Tectu  
> Created at: 2021-07-18 10:55:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2291#issuecomment-882037864  

> One could envisage a higher level library, built on top of beast that did so.  
  
That is what I am working on ->https://github.com/Tectu/malloy  
I just don't want to add stuff that is already provided by beast so occasionally I have to drop by here and ask things like that ;-)

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-07-18 11:04:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2291#issuecomment-882038927  

When I get a moment, I intend to create a complimentary library, both on the server and client side.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-07-18 15:05:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2291#issuecomment-882070539  

There are some algorithms for parsing such lists, e.g.:  
https://github.com/boostorg/beast/blob/710cc53331f197f6f17e8c38454c09df68e43c03/include/boost/beast/http/rfc7230.hpp#L21
