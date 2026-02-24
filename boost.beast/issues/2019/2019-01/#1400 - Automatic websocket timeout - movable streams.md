# #1400 - Automatic websocket timeout / movable streams [Closed]

> Username: vinniefalco  
> Created at: 2019-01-01 19:45:28 UTC  
> Updated at: 2019-02-23 03:09:59 UTC  
> Closed at: 2019-02-23 03:09:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1400  

`websocket::stream` can have automatic timeout with ping and socket closure behavior if we copy the style from `beast::stream_socket`. With the stream state in a shared pointer, we can also allow moves and destruction while operations are outstanding.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-23 03:09:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1400#issuecomment-466610876  

This is done
