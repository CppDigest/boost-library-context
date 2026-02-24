# #2225 - Seeing websocket handshake failue with end of stream when connecting to echo.websocket,org both on SSL and TCP.  Code is very similar to the client example [Closed]

> Username: gopalak  
> Created at: 2021-05-06 15:25:19 UTC  
> Updated at: 2021-05-06 19:59:55 UTC  
> Closed at: 2021-05-06 19:58:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2225  

using boost 1.75 on CentOS7 and beast version is 306. Appreciate the help. If error code is needed I only have the error message but if one can tell me how to get error code of beast will change and post it here

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-06 15:29:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2225#issuecomment-833616046  

Can you share a github project so I can reproduce?

---

## Comment 2

> Username: gopalak  
> Created at: 2021-05-06 15:31:24 UTC  
> Updated at: 2021-05-06 15:32:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2225#issuecomment-833618151  

I dont. Its an official one. So its not in here :( and its on CentOS7

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-05-06 15:35:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2225#issuecomment-833621016  

would you be able to share with me a minimal complete example of the connect issue? It would probably require about 50 lines of code in one .cpp file to reduce it to the minimum number of steps to reproduce.

---

## Comment 4

> Username: gopalak  
> Created at: 2021-05-06 15:37:31 UTC  
> Updated at: 2021-05-06 15:38:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2225#issuecomment-833622670  

Sure will try it to redact and paste it here.  Give like 15 min. I am not sure if that small one may exhibit the same issue but worth it

---

## Comment 5

> Username: gopalak  
> Created at: 2021-05-06 19:58:16 UTC  
> Updated at: 2021-05-06 19:59:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2225#issuecomment-833819827  

Hey so I figured the issue - some how my URI path was messed up and instead of / it had some other data member. So once I found that and fixed the WS handshake went thro fine. Thanks  
  
Cheers  
Gops  
  
PS: Apologies for the delay got pulled into some meetings.

---

## Comment 6

> Username: gopalak  
> Created at: 2021-05-06 19:58:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2225#issuecomment-833819998  

I am marking it closed.
