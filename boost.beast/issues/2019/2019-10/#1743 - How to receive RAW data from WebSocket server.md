# #1743 - How to receive RAW data from WebSocket server [Closed]

> Username: alorver  
> Created at: 2019-10-23 11:27:05 UTC  
> Updated at: 2019-11-29 13:58:41 UTC  
> Closed at: 2019-11-29 13:58:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1743  

We need to receive raw data from WebSocket server, including non printable chars. We faced with case when WebSocket server as responce uploads json-file, but it is not equal to source json-file, as we have noticed without some chars like "\n" (probably anything else).   
  
We use simple code:  
  
#Dest: Windows Dll (Win7/32)  
#WebSocket Sync  
beast::flat_buffer buffer;  
str::stringres res= _strdup(beast::buffers_to_string(buffer.data()).c_str());  
Boost ver: 1.71  
  
Is there any way to receive absolutly equal json-file to source one from WebSocket server?  
  
Best regards,  
Andrey

---

## Comment 1

> Username: reddwarf69  
> Created at: 2019-10-23 11:34:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1743#issuecomment-545400888  

As long as you set the stream to send binary messages (https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/binary/overload1.html) you can send whatever you want and will receive absolutely equal data.  
  
The "simple code" you included doesn't show either how you send or receive data, so it's not specially useful. If you need help with your code you are going to need to include a self-contained full example. You can easily create one using https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/ref/boost__beast__test__stream.html to avoid the connection boilerplate.

---

## Comment 2

> Username: alorver  
> Created at: 2019-10-23 13:15:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1743#issuecomment-545437702  

Thank you, we need to do some checks regarding JSON-parsing (using node.js/JSON.stringify(...)) on our WebServer(WebSocket). According to your reply,  everything rest is right.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-11-22 13:53:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1743#issuecomment-557539831  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-11-29 13:58:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1743#issuecomment-559798585  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
