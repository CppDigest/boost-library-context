# #241 - Implement read_message_max for compressed streams [Closed]

> Username: vinniefalco  
> Created at: 2017-01-28 00:58:12 UTC  
> Updated at: 2017-09-01 03:07:52 UTC  
> Closed at: 2017-09-01 03:07:52 UTC  
> Url: https://github.com/boostorg/beast/issues/241  

When permessage-deflate is active on a stream, the calculation for determining the message size became more complex, and broke the read_message_max option for compressed streams.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-09 00:40:16 UTC  
> Url: https://github.com/boostorg/beast/issues/241#issuecomment-307264457  

Callers can just set the maximum on their dynamic buffer, we might not even need the `read_message_max` feature.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-27 22:55:25 UTC  
> Url: https://github.com/boostorg/beast/issues/241#issuecomment-318509218  

I believe this is done, it just needs tests now.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-09-01 03:07:52 UTC  
> Url: https://github.com/boostorg/beast/issues/241#issuecomment-326476099  

Works!
