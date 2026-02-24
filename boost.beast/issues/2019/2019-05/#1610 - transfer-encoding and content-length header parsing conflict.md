# #1610 - transfer-encoding and content-length header parsing conflict [Open]

> Username: ovenpasta  
> Created at: 2019-05-14 18:52:47 UTC  
> Updated at: 2019-06-13 20:17:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1610  

I've found an annoying bug in beast http client when handling chunked encoding...  
Some servers send both content-length and transfer-encoding: chunked  
Currently beast raises an error if both headers are present.  
  
https://greenbytes.de/tech/webdav/rfc2616.html#message.length  
  
>   1.  If a Content-Length header field (Section 14.13) is present, its decimal value in OCTETs represents both the entity-length and the transfer-length. The Content-Length header field MUST NOT be sent if these two lengths are different (i.e., if a Transfer-Encoding header field is present). If a message is received with both a Transfer-Encoding header field and a Content-Length header field, the latter MUST be ignored.  
  
https://github.com/boostorg/beast/blob/d1dabebd8352ce0e854474837ed11a27cb688edc/include/boost/beast/http/impl/basic_parser.ipp#L794-L799  
  
https://github.com/boostorg/beast/blob/d1dabebd8352ce0e854474837ed11a27cb688edc/include/boost/beast/http/impl/basic_parser.ipp#L825-L830

---

## Comment 1

> Username: cmazakas  
> Created at: 2019-05-14 19:50:17 UTC  
> Updated at: 2019-05-14 19:50:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1610#issuecomment-492383924  

It seems like you're using an out-of-date RFC.  
  
The newest list can be found here:  
https://www.w3.org/Protocols/  
  
Specifically, you'll want: https://tools.ietf.org/html/rfc7230#section-3.3.3  
  
       If a Transfer-Encoding header field is present in a response and  
       the chunked transfer coding is not the final encoding, the  
       message body length is determined by reading the connection until  
       it is closed by the server.  If a Transfer-Encoding header field  
       is present in a request and the chunked transfer coding is not  
       the final encoding, the message body length cannot be determined  
       reliably; the server MUST respond with the 400 (Bad Request)  
       status code and then close the connection.  
  
Emphasis on: "ought to be handled as an error."  
  
Beast is _technically_ in the right here but to support non-conforming servers in the wild, this could be configured to be optional behavior.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2019-06-13 20:08:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1610#issuecomment-501859972  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-06-13 20:17:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1610#issuecomment-501862852  

The Beast parser should have a way to inform the caller when non-compliant HTTP is encountered, so that a server can deliver a 400 response.
