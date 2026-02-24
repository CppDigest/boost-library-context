# #3061 - When working with json on websocket server, it only needs to pass the websocket buffer to the clients connected to the server? [Closed]

> Username: GyroPower  
> Created at: 2025-11-28 21:41:49 UTC  
> Updated at: 2025-12-02 06:34:37 UTC  
> Closed at: 2025-12-02 06:34:37 UTC  
> Url: https://github.com/boostorg/beast/issues/3061  

I would like to use json for the client side of the little project that I'm basing from the examples of the library but I was wondering If I only have to handle the json part from the client side or do the server handle it too and just not only redirect the message to the clients connected to the session, to handle binary data (images, audio or video) and the text that can come with the message or only the client side has to deal with it?, or there is something that I'm missing from the docs that explains this situation, and thanks if you have some feed back to give me or if you only read this and don't treat me awful :); and I'm asking and maybe someone can point directly where to look or a hit of how to deal with json on a websocket context on beast, mean while I will look for that on my own, and maybe I wrote this weird, English is not my first language.

---

## Comment 1

> Username: GyroPower  
> Created at: 2025-12-02 06:34:37 UTC  
> Url: https://github.com/boostorg/beast/issues/3061#issuecomment-3600416861  

Sorry, I was a bit overwhelmed for thinking about it, I knew what json was but I did not use it much so I was like, it's a special stuff to use but It can be send it as text and I can code the image to base64 so, sorry if the question was silly, sorry.
