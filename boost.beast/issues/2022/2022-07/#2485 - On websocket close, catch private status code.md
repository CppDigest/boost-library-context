# #2485 - On websocket close, catch private status code [Closed]

> Username: jbaumgarten  
> Created at: 2022-07-19 18:06:59 UTC  
> Updated at: 2022-07-19 19:08:06 UTC  
> Closed at: 2022-07-19 19:08:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2485  

Sir, Madam,  
  
I'm using boost library, and beast library for websockets.  
I need to catch private status code when the connexion is closed.  
  
I read again and again the headers, but I don't find a way to do it.  
Did I miss something?  
  
Yours sincerely,  
Julien

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-07-19 18:15:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2485#issuecomment-1189405742  

Call `websocket::stream::reason` after receiving `error::closed` to get the custom code:  
https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/reason.html

---

## Comment 2

> Username: jbaumgarten  
> Created at: 2022-07-19 19:03:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2485#issuecomment-1189450436  

Thx man. That works great :)
