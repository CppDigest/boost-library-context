# #879 - what's the meaning of ws.text() and ws.get_text()? [Closed]

> Username: liuaifu  
> Created at: 2017-11-10 12:59:22 UTC  
> Updated at: 2017-11-11 02:56:03 UTC  
> Closed at: 2017-11-11 02:56:03 UTC  
> Url: https://github.com/boostorg/beast/issues/879  

```  
ws.read(buffer);  
ws.text(ws.got_text());  
ws.write(buffer.data());  
```  
what's the meaning of ws.text() and ws.get_text()?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-10 15:46:29 UTC  
> Url: https://github.com/boostorg/beast/issues/879#issuecomment-343508159  

`text()`  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/text/overload1.html  
  
`got_text()`  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/got_text.html
