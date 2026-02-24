# #1482 - Buffer overflow on ssl WebSocket handshake [Closed]

> Username: haashirashraf  
> Created at: 2019-02-28 00:07:53 UTC  
> Updated at: 2019-02-28 00:14:09 UTC  
> Closed at: 2019-02-28 00:14:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1482  

Hey Vinny,   
  
First of all thanks for making the beast library, recently I've tried to connect to the crypto facilities WebSocket, I'm using Beast version 167 with Xcode 10, using your ssl WebSockets example with the initial parameters of "www.cryptofacilities.com", 443 and "{\"event\": \"subscribe\",\"feed\": \"book\",\"product_ids\": [\"PI_XBTUSD\"]}" for the host, port and text arguments respectively produces a buffer overflow error at the handshake stage, maybe this is a bug in the library (to confirm at the handshake stage I am passing in the host argument as given above and the endpoint has been modified to "/ws/v1" as required). You should be able to reproduce the error with the above information, I hope to hear back from you soon.  
  
To clarify I have independently validated using websocket.org that the crypto facility WebSockets is in fact working.  
  
Thanks,  
  
Haashir

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-28 00:13:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1482#issuecomment-468083448  

This is fixed in the upcoming Boost 1.70

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-02-28 00:14:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1482#issuecomment-468083573  

Duplicate of: https://github.com/boostorg/beast/issues/1460
