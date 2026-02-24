# #1952 - Need a class can point to both `websocket::stream<beast::tcp_stream>` and `websocket::stream<beast::ssl_stream<beast::tcp_stream>>` [Closed]

> Username: nineKnight  
> Created at: 2020-05-13 08:59:00 UTC  
> Updated at: 2024-01-08 19:21:24 UTC  
> Closed at: 2020-05-15 07:37:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1952  

I'm writing a websocket server referring to `websocket_server_coro.cpp` and `websocket_server_coro_ssl.cpp`. I wanna set a flag to switch SSL, but I find no same base class in `websocket::stream<beast::tcp_stream>` and `websocket::stream<beast::ssl_stream<beast::tcp_stream>>`. I don't know if I miss such a class.  
Anyone provides clues is welcomed.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-05-13 11:17:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1952#issuecomment-627916381  

https://github.com/boostorg/beast/blob/fc7c645f64192976acfb6a88a8fdbd5d92bc44fc/example/advanced/server-flex/advanced_server_flex.cpp#L251

---

## Comment 2

> Username: nineKnight  
> Created at: 2020-05-13 12:02:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1952#issuecomment-627936237  

Wow! It seems this is what I need. Thank you.  
However, if I'm using sync APIs, I get no way to use a base-class pointer to manage `websocket::stream<beast::tcp_stream>` or `websocket::stream<beast::ssl_stream<beast::tcp_stream>>`, right?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-05-13 12:04:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1952#issuecomment-627937121  

The CRTP technique (Curiously Recurring Template Pattern) works with any API, synchronous or otherwise.

---

## Comment 4

> Username: nineKnight  
> Created at: 2020-05-13 13:55:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1952#issuecomment-628003152  

No. I just want a class like this:  
```c++  
class Stream {  
public:  
    void Read(...);  
    void Write(...);  
  
    void PostDetectSSL(bool result...) {  
        if (result) {  
            // TODO: ws_.reset(new websocket::stream<beast::ssl_stream<beast::tcp_stream>>(...));  
        } else {  
            // TODO: ws_.reset(new websocket::stream<beast::tcp_stream>(...));  
        }  
    }  
protected:  
    std::unique_ptr<websocket::stream<beast::base_stream>> ws_;  
}  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-05-13 14:34:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1952#issuecomment-628031386  

Okay, then write it

---

## Comment 6

> Username: nineKnight  
> Created at: 2020-05-14 02:27:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1952#issuecomment-628347783  

Mmmm...I find no sush a `beast::base_stream` class and it doesn't exist indeed, right? Could you share your idea why you don't need a sush a `beast::base_stream` class?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-05-14 02:33:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1952#issuecomment-628349319  

Because Beast demonstrates how to do it with CRTP, see:  
https://github.com/boostorg/beast/blob/fc7c645f64192976acfb6a88a8fdbd5d92bc44fc/example/advanced/server-flex/advanced_server_flex.cpp#L251

---

## Comment 8

> Username: nineKnight  
> Created at: 2020-05-14 02:52:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1952#issuecomment-628354875  

So you mean everyone using `beast` has to force the code to be under the CRTP or rewrite `websocket::stream`.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2020-05-14 03:05:04 UTC  
> Updated at: 2020-05-14 03:06:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1952#issuecomment-628358369  

No one is "forced" to do anything. I'm saying that if you want to implement your own stream which can work as either an SSL stream or a plain stream, then that's fine, and such a type will work with `websocket::stream` as long as it meets the requirements of _SyncReadStream_ and _SyncWriteStream_ for synchronous operations, and _AsyncReadStream_ and _AsyncWriteStream_ for asynchronous operations. But Beast does not provide such a type.

---

## Comment 10

> Username: nineKnight  
> Created at: 2020-05-14 03:39:11 UTC  
> Updated at: 2020-05-14 05:56:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1952#issuecomment-628367363  

Maybe I can try maintaining a `beast::tcp_stream` and keeping the result of `detect_ssl`, and constructing a `websocket::stream` when `read()` or `write()`.  
  
Update:  
It doesn't work.

---

## Comment 11

> Username: nineKnight  
> Created at: 2020-05-15 03:24:16 UTC  
> Updated at: 2020-05-15 04:25:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1952#issuecomment-629004421  

It's annoying that I have to maintain both `websocket::stream<beast::tcp_stream>` and `websocket::stream<beast::ssl_stream<beast::tcp_stream>>` and keeping the result of `detect_ssl` when using `asio::spawn`. If there's a base, the code can be much simpler.  
  
> But Beast does not provide such a type.  
  
That's a pity.  
  
I think SSL is an attachment to stream but beast makes it another stream. I prefer a switch like `set_ssl(...)` which may be more code-friendly.

---

## Comment 12

> Username: madmongo1  
> Created at: 2020-05-15 12:45:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1952#issuecomment-629215536  

This is something we get asked for fairly frequently. It may not be theoretically correct, but I agree that users would find it useful.

---

## Comment 13

> Username: hjelmeland  
> Created at: 2024-01-08 19:21:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1952#issuecomment-1881687331  

> It's annoying that I have to maintain both `websocket::stream<beast::tcp_stream>` and `websocket::stream<beast::ssl_stream<beast::tcp_stream>>` and keeping the result of `detect_ssl` when using `asio::spawn`. If there's a base, the code can be much simpler.  
>   
> I think SSL is an attachment to stream but beast makes it another stream. I prefer a switch like `set_ssl(...)` which may be more code-friendly.  
  
Late to the party, but I just made such a wrapper class : https://github.com/hjelmeland/BeastTlsOrTcpStream. For those of you that don't mind wasting some memory on SSL when using it in TCP mode, feel free to use it.
