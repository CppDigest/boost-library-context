# #816 - URL path after handshake? [Closed]

> Username: bitm4ster  
> Created at: 2017-10-16 08:38:27 UTC  
> Updated at: 2017-10-16 13:58:44 UTC  
> Closed at: 2017-10-16 09:59:01 UTC  
> Url: https://github.com/boostorg/beast/issues/816  

I'm building a websocket server which handles clients differently based on the URL path supplied by the client during the handshaking process. I've been digging around in stream.hpp for a bit and can't seem to find a way to retrieve the URL path after the handshaking process has completed. Is this not a feature?  
  
Thanks for any help!

---

## Comment 1

> Username: bitm4ster  
> Created at: 2017-10-16 09:58:57 UTC  
> Updated at: 2017-10-16 10:01:08 UTC  
> Url: https://github.com/boostorg/beast/issues/816#issuecomment-336839449  

Found it! https://github.com/boostorg/beast/issues/751

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-10-16 13:58:44 UTC  
> Url: https://github.com/boostorg/beast/issues/816#issuecomment-336895425  

Excellent!
