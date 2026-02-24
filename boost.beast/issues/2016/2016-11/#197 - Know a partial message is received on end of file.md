# #197 - Know a partial message is received on end of file? [Closed]

> Username: vinniefalco  
> Created at: 2016-11-21 14:29:43 UTC  
> Updated at: 2017-02-18 00:07:33 UTC  
> Closed at: 2017-02-18 00:07:33 UTC  
> Url: https://github.com/boostorg/beast/issues/197  

During HTTP read if the end of file is reached in the middle of a message, should we return `error::eof` to the caller or should it be handled as a parsing error, and then return `error::eof` on the next call to read?

---

## Comment 1

> Username: djarek  
> Created at: 2016-11-22 03:46:13 UTC  
> Updated at: 2016-11-22 03:47:03 UTC  
> Url: https://github.com/boostorg/beast/issues/197#issuecomment-262140191  

Receiving an eof does not necessarily mean the connection entered an "invalid" state. A peer (this mostly true for clients) can call ```shutdown()``` on their write end of the socket as a way to indicate end of message to the other peer (as an alternative to providing Content-length, this is mostly used for backwards compatibility with HTTP/1.0 or dumb implementations that say they are HTTP/1.1 compliant but actually aren't). So this input:  
```  
POST /do-stuff HTTP/1.1  
Host: myhost.com  
Content-type: application/json  
  
{"hello" : "world"}  
[EOF]  
```  
is a "valid" HTTP message. Of course, the same is true if the request body were empty. I think the only situation where you should treat EOF as an error that has to be returned after parsing is if we encounter:  
  
1. EOF before final header line.  
2. EOF before ```Content-length``` bytes are transferred (assuming ```Content-length``` is present in the header).  
  
This might be obvious, but another "valid" message is this:  
```  
POST /do-stuff HTTP/1.1  
Host: myhost.com  
Content-type: application/json  
Content-length: 2  
  
{}  
[EOF]  
```  
  
There's a reason why I put the word "valid" in quotes. They're not really valid if we want to be pedantic about RFC 7230's [message length](https://tools.ietf.org/html/rfc7230#section-3.3.3):  
  
> A server MAY reject a request that contains a message body but not a  
   Content-Length by responding with 411 (Length Required).  
  
The emphasis on the word MAY means that a general-purpose library like Beast should (IMO) consider such messages to be valid.

---

## Comment 2

> Username: viccpp  
> Created at: 2016-11-23 07:21:46 UTC  
> Url: https://github.com/boostorg/beast/issues/197#issuecomment-262447818  

Some info about using half-close for request-messages in practice:  
  
http://stackoverflow.com/questions/14069379/does-http-1-1-allow-delimiting-end-of-request-by-half-closing-the-connection  
http://mailman.nginx.org/pipermail/nginx/2008-September/007358.html  
  
tl;dr: seems like it's a weird scenario

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-02-18 00:07:33 UTC  
> Url: https://github.com/boostorg/beast/issues/197#issuecomment-280800662  

Its going to be a parse error, returning `error::partial_message`. Which implies **eof**.
