# #248 - Websocket ping callback [Closed]

> Username: Codeazure  
> Created at: 2017-02-03 01:57:08 UTC  
> Updated at: 2017-02-07 20:23:41 UTC  
> Closed at: 2017-02-07 20:23:41 UTC  
> Url: https://github.com/boostorg/beast/issues/248  

I would like to be able to set a ping control frame callback. This is so the client can tell if it is still receiving them from the server periodically by using a timer set when the last ping was received. I don't need to do anything in Beast when this happens - I'm happy with the auto-pong functionality. I just want to know when it happens.  
  
At present, the only strategy I can see is that both client and server send periodic ping messages to confirm the other side is still there when the pong is received. Is this the common approach for most WebSocket applications?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-02-03 02:06:35 UTC  
> Url: https://github.com/boostorg/beast/issues/248#issuecomment-277145160  

We can do one of two things:  
1. Add a "ping callback", or  
2. Call the pong callback when a ping is received. The pong callback could have an aditional bool indicating if the control frame is a ping versus a pong.

---

## Comment 2

> Username: Codeazure  
> Created at: 2017-02-03 02:13:48 UTC  
> Url: https://github.com/boostorg/beast/issues/248#issuecomment-277146093  

Either one would be great. With option 2, would you change its name to on_ping_pong() in that case or just keep the current on_pong? Maybe cleaner to go with option 1, because then you don't break existing applications and then the two callbacks are clearly named.  
  
I don't mind either solution - you choose which suits the design best.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-02-03 21:28:09 UTC  
> Url: https://github.com/boostorg/beast/issues/248#issuecomment-277366870  

Please give this a try  
https://github.com/vinniefalco/Beast/commits/b27

---

## Comment 4

> Username: Codeazure  
> Created at: 2017-02-05 23:14:51 UTC  
> Url: https://github.com/boostorg/beast/issues/248#issuecomment-277558372  

Thanks for doing that so quickly. I'll test that as soon as possible.  
  
In the websocket documentation, it says : "'is_pong' set to 'true' if a ping was received". I assume this is a typo & you meant the other way around?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-02-05 23:32:30 UTC  
> Url: https://github.com/boostorg/beast/issues/248#issuecomment-277559520  

It should read "is_pong is set to `true` if a pong was received`

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-02-05 23:46:23 UTC  
> Url: https://github.com/boostorg/beast/issues/248#issuecomment-277560337  

I fixed the documentation (thanks!), here's a preview:  
http://vinniefalco.github.io/stage/beast/
