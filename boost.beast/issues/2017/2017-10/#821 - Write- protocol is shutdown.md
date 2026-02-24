# #821 - [Question] Write: protocol is shutdown [Closed]

> Username: no-realm  
> Created at: 2017-10-20 22:46:30 UTC  
> Updated at: 2024-12-24 16:22:14 UTC  
> Closed at: 2017-10-21 02:14:24 UTC  
> Url: https://github.com/boostorg/beast/issues/821  

Hello,  
  
I am basing my code on your crawl example: https://github.com/boostorg/beast/blob/develop/example/http/client/crawl/http_crawl.cpp  
  
I tried to convert the code to use https, instead of http, but when I use  
```c++  
stream_.shutdown(ec);  
```  
the next request fails with the error: `Write: protocol is shutdown`  
  
And using  
```c++  
stream_.next_layer().shutdown(boost::asio::socket_base::shutdown_both, ec);  
stream_.next_layer().close(ec);  
```  
results in  
```  
Read: wrong version number  
Read: internal error  
Read: internal error  
[...]  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-21 00:54:45 UTC  
> Url: https://github.com/boostorg/beast/issues/821#issuecomment-338352704  

Are you calling `ssl::stream::handshake`? Can you post the program?

---

## Comment 2

> Username: no-realm  
> Created at: 2017-10-21 01:09:45 UTC  
> Url: https://github.com/boostorg/beast/issues/821#issuecomment-338353691  

Yes I am calling handshake().  
  
Anyway, here is the program.  
[requester.zip](https://github.com/boostorg/beast/files/1403659/requester.zip)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-21 01:31:00 UTC  
> Url: https://github.com/boostorg/beast/issues/821#issuecomment-338354949  

I don't think you can re-use the `ssl::stream`, you have to destroy it and recreate it. You can store the stream in a `boost::optional` and call `optional::emplace` to achieve that.

---

## Comment 4

> Username: no-realm  
> Created at: 2017-10-21 01:32:26 UTC  
> Url: https://github.com/boostorg/beast/issues/821#issuecomment-338355037  

Ok I will try that.  
Where should I call emplace?  
Right after shutdown or when I get the next request.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-10-21 01:36:53 UTC  
> Url: https://github.com/boostorg/beast/issues/821#issuecomment-338355289  

Call `emplace` before you establish a new connection

---

## Comment 6

> Username: no-realm  
> Created at: 2017-10-21 02:06:16 UTC  
> Updated at: 2017-10-21 02:12:31 UTC  
> Url: https://github.com/boostorg/beast/issues/821#issuecomment-338356840  

It works now, but it is very slow :/  
It seems like creating a new ssl stream instance is very slow?  
Anyway thanks for your help :)  
  
Edit: Never mind. It's because I bombarded the server too much. It seems to limit the throughput now ^^

---

## Comment 7

> Username: abhiarora4  
> Created at: 2019-12-11 14:04:26 UTC  
> Url: https://github.com/boostorg/beast/issues/821#issuecomment-564556520  

Can you help me in getting the same thing for boost websocket? I am using 1.68 version.

---

## Comment 8

> Username: StavrosMar  
> Created at: 2024-12-24 02:28:59 UTC  
> Url: https://github.com/boostorg/beast/issues/821#issuecomment-2560552782  

i see some commits , is it fixed now or need to create a new object if i need to reconnect ?

---

## Comment 9

> Username: ashtum  
> Created at: 2024-12-24 05:12:08 UTC  
> Url: https://github.com/boostorg/beast/issues/821#issuecomment-2560661744  

> i see some commits , is it fixed now or need to create a new object if i need to reconnect ?  
  
[websocket::stream::async_close](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html) and [websocket::stream::close](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/close.html) close the underlying stream once the close procedure is complete. you need to create a new stream.

---

## Comment 10

> Username: StavrosMar  
> Created at: 2024-12-24 16:22:13 UTC  
> Url: https://github.com/boostorg/beast/issues/821#issuecomment-2561270178  

ok thanks, maybe somethiing to add in the future as I wanted to avoid that
