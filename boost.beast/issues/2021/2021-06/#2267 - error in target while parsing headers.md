# #2267 - error in target while parsing headers [Closed]

> Username: quazeeee  
> Created at: 2021-06-28 20:36:11 UTC  
> Updated at: 2021-06-28 21:27:49 UTC  
> Closed at: 2021-06-28 20:55:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2267  

Hello. I have been using boost::beast for years without flaws, a great project. But now I am facing trouble. I am developing software for windows and copied some part of the old code with the standard beast WebSocket client and server. On the server side, during the accept, I am facing an issue in parse_target method.  
  
parthe_method successfully handles "GET", but pare_target begins work from "HTTP/1.1" and finally fail on "\r\n" after HTTP/1.1   
  
Can you provide any suggestions?   
  
Headers from debug:  
  
GET HTTP/1.1  
Host: 127.0.0.1:9092  
Upgrade: websocket  
Connection: upgrade  
Sec-WebSocket-Key: zu/EYa2Fgv3M0U5J0ho2Rw==  
Sec-WebSocket-Version: 13  
Sec-WebSocket-Protocol: test_secure  
  
client side code:  
wss->async_handshake("127.0.0.1:9092", "/", bind_front ...)  
  
boost beast version: 1.75

---

## Comment 1

> Username: quazeeee  
> Created at: 2021-06-28 20:55:53 UTC  
> Updated at: 2021-06-28 20:57:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2267#issuecomment-870035165  

Actually, due to some specific manipulations "/" in the path was omited.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-06-28 21:27:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2267#issuecomment-870056752  

Glad I could help :)
