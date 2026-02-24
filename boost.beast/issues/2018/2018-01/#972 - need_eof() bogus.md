# #972 - need_eof() bogus? [Closed]

> Username: chrisics  
> Created at: 2018-01-09 14:28:46 UTC  
> Updated at: 2018-01-09 15:22:31 UTC  
> Closed at: 2018-01-09 15:22:17 UTC  
> Url: https://github.com/boostorg/beast/issues/972  

The implemention for need_eof() seems bogus:  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/ref/boost__beast__http__message/need_eof.html  
  
A POST should return status 201 Created, and keep the connection open if requested, should it not?    
  
See https://tools.ietf.org/html/rfc7230#section-6.3  
  
Br,  
Chris.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-09 15:02:03 UTC  
> Url: https://github.com/boostorg/beast/issues/972#issuecomment-356308885  

From https://tools.ietf.org/html/rfc7230#section-3.3  
  
*"All 1xx (Informational), 204 (No Content), and 304 (Not Modified) responses do not include a message body.  **All other responses do include a message body, although the body might be of zero length.**"*  
  
I believe the server is required to send "Content-Length: 0" when it sends a 201 status response to a POST request.

---

## Comment 2

> Username: chrisics  
> Created at: 2018-01-09 15:09:20 UTC  
> Url: https://github.com/boostorg/beast/issues/972#issuecomment-356311093  

all right, Thanks.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-01-09 15:10:18 UTC  
> Url: https://github.com/boostorg/beast/issues/972#issuecomment-356311391  

Let's not be too hasty in closing this, I've asked a few questions on StackOverflow to verify

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-01-09 15:22:17 UTC  
> Updated at: 2018-01-09 15:22:31 UTC  
> Url: https://github.com/boostorg/beast/issues/972#issuecomment-356315137  

Okay, I am convinced that in the absence of "Content-Length: 0" or "Transfer-Encoding: chunked" an **eof** is needed.  
  
This implementation always sends "Content-Length: 0" for 201 status responses lacking an entity body:  
  
https://github.com/square/retrofit/issues/1721
