# #2973 - How many live websock connections will this amount of RAM hold [Closed]

> Username: shivank1111  
> Created at: 2025-01-20 10:06:35 UTC  
> Updated at: 2025-02-03 07:13:37 UTC  
> Closed at: 2025-02-03 07:13:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2973  

Boost Version 354  
  
  
I wanted to know how many live connections will "16GB" of RAM will hold.

---

## Comment 1

> Username: ashtum  
> Created at: 2025-01-21 07:54:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2973#issuecomment-2603889848  

You need to consider the size of all buffers used for read and write operations, plus the size of the stream object. Additionally, `beast::websocket::stream` allocates an internal buffer with a default size of 4096, which can be overridden using [websocket::stream::write_buffer_bytes](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/write_buffer_bytes/overload1.html).  
If you enable compression, there will be additional internal allocation for [zlib::deflate_stream](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__zlib__deflate_stream.html) and [zlib::inflate_stream](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__zlib__inflate_stream.html). This allocation is difficult to estimate beforehand, but you may be able to determine it pragmatically.    
  
If compression is not essential for your application, you might consider disabling it: [websocket::stream::set_option (3 of 3 overloads)](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/set_option/overload3.html).
