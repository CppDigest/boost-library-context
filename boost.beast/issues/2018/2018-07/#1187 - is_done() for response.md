# #1187 - is_done() for response [Closed]

> Username: asvald  
> Created at: 2018-07-12 13:58:10 UTC  
> Updated at: 2018-08-18 15:52:20 UTC  
> Closed at: 2018-08-18 15:52:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1187  

BOOST_BEAST_VERSION 167  
  
Hello! How to deal with such responses, for ex.  
from ebay.com  
  
> HTTP/1.1 301 Moved Permanently  
> Location: https://www.ebay.com  
> \r\n\r\n  
  
or from apple.com  
  
> HTTP/1.1 301 MOVED PERMANENTLY  
> Server: Apache   
> Date: Thu, 12 Jul 2018 13:43:28 GMT  
> Location: https://www.apple.com/  
> Content-type: text/html  
> Connection: close  
> \r\n\r\n  
  
I omitted \r\n at the end of fields.  
is_done() for  
```  
boost::beast::http::response_parser<  
			boost::beast::http::string_body>>  
```  
returns false for such responses.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-12 14:14:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1187#issuecomment-404526953  

Are you asking how to handle a 301 status code? You need to inspect the Location field and resubmit the request using a new connection to the indicated host, possibly over SSL (the "https").

---

## Comment 2

> Username: asvald  
> Created at: 2018-07-12 14:18:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1187#issuecomment-404528119  

I corrected my first post. I mean is_done() false for this responses.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-07-12 14:37:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1187#issuecomment-404534960  

Well, the behavior of `parser` is correct in this case. There is no Content-Length, so the end of the message body is determined by receiving a FIN or SSL shutdown handshake. The server should close the connection, which will make the parser return `true` for `is_done`. If you want to speed this up you can check if the response is a 301, and then call `is_header_done` instead, to break early.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-08-11 15:08:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1187#issuecomment-412281103  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-08-18 15:52:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1187#issuecomment-414067732  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
