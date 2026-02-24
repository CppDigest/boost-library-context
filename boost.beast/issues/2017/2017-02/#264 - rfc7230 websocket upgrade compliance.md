# #264 - rfc7230 websocket upgrade compliance [Closed]

> Username: vinniefalco  
> Created at: 2017-02-11 13:47:36 UTC  
> Updated at: 2022-10-13 01:26:11 UTC  
> Closed at: 2022-10-13 01:26:11 UTC  
> Url: https://github.com/boostorg/beast/issues/264  

_If a server receives both an Upgrade and an Expect header field with the "100-continue" expectation (Section 5.1.1 of [RFC7231]), the server MUST send a 100 (Continue) response before sending a 101 (Switching Protocols) response._  
https://tools.ietf.org/html/rfc7230#section-6.7

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-01 03:09:53 UTC  
> Updated at: 2017-09-01 03:11:34 UTC  
> Url: https://github.com/boostorg/beast/issues/264#issuecomment-326476288  

The handshake logic has to check if the decorator set "Expect: 100-continue", and know to read the Continue response first. We might want to serialize the optional Continue response and the required Upgrade response into a single buffer and just send that. See #761
