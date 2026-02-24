# #1686 - Ability to read request headers during "WebSocket upgrade" [Closed]

> Username: VadymBezdushnyi  
> Created at: 2019-08-22 11:36:46 UTC  
> Updated at: 2019-09-29 19:28:46 UTC  
> Closed at: 2019-09-29 19:28:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1686  

In my application I need an ability to read custom headers from websocket handshake.  
  
Possible workarounds discussed here: https://github.com/boostorg/beast/issues/1442 https://github.com/boostorg/beast/issues/501 https://github.com/boostorg/beast/issues/751  
  
With current implementation developer needs to create both http session and websocket session. This is an overhead for websocket-only server.  
  
In other libraries this issue is solved by saving http request for each connection, some examples:  
- [Websocket++](https://www.zaphoyd.com/static/websocketpp/docs/classwebsocketpp_1_1role_1_1server_1_1connection.html#a2f3d67c7302bc105755696811299e3e4)  
- [QtWebsocket](https://doc.qt.io/qt-5/qwebsocket.html#request)   
  
However, saving upgrade request directly is not compatible with `boost::beast` design.  
  
My idea is to make an overload of async_accept function similar to [this one](https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_accept/overload3.html  
). But instead of passing already created request, pass a reference where request will be stored after handshake.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-08-22 13:22:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1686#issuecomment-523903981  

> saving upgrade request directly is not compatible with boost::beast design.  
  
Why not? Just read the request yourself using Beast (e.g. `beast::http::read`) and then if it is a WebSocket Upgrade request (i.e. `beast::websocket::is_upgrade` returns `true`) call the overload of accept which lets you pass the message:  
https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/accept/overload6.html  
  
This is demonstrated in the advanced examples:  
https://github.com/boostorg/beast/blob/6d614cf9a8c6d81258e309682621286bf9d74ec3/example/advanced/server/advanced_server.cpp#L482

---

## Comment 2

> Username: VadymBezdushnyi  
> Created at: 2019-08-23 14:33:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1686#issuecomment-524338488  

Just to clarify everything, is it true that these two snippets work similar?  
_Standart accept:_  
```  
ws_.async_accept([this](beast::error_code ec)  
{  
    if (ec) return;  
    OnHandshake();  
});  
```  
_Accept with saving request:_  
```  
// _req is a class member  
flat_buffer read_buffer;  
  
http::async_read(ws_.next_layer(), read_buffer, req_, [this](beast::error_code ec)  
{  
    if (ec) return;  
    ws_.async_accept(_req, [this](beast::error_code ec)  
    {  
        if (ec) return;  
        OnHandshake();  
    });  
});  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-08-23 18:43:50 UTC  
> Updated at: 2019-08-23 18:44:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1686#issuecomment-524421716  

Yes that looks right. `read_buffer` has to be a class member as well.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-09-22 18:44:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1686#issuecomment-533907212  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2019-09-29 19:28:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1686#issuecomment-536333260  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
