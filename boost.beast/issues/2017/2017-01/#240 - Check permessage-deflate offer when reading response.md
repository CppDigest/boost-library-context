# #240 - Check permessage-deflate offer when reading response [Open]

> Username: vinniefalco  
> Created at: 2017-01-28 00:56:28 UTC  
> Updated at: 2017-11-18 21:03:21 UTC  
> Url: https://github.com/boostorg/beast/issues/240  

Websocket streams operating in client mode need to check the offer returned by the server to verify that it meets the criteria, otherwise the handshake function should return an error.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-18 21:03:21 UTC  
> Url: https://github.com/boostorg/beast/issues/240#issuecomment-345471014  

See #264
