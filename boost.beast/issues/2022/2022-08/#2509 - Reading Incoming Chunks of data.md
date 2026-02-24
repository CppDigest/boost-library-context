# #2509 - Reading Incoming Chunks of data [Closed]

> Username: PasinduKollure  
> Created at: 2022-08-25 23:32:22 UTC  
> Updated at: 2022-09-02 18:56:00 UTC  
> Closed at: 2022-09-02 18:56:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2509  

Hi,  
  
I am creating a SSL HTTP client which sends chunks of data (`Transfer-Encoding: chunked`) and the server sends back processed chunks of data as the server is receiving raw chunks. I want to read the processed chunks simultaneously and this communication link between the client and server is full duplex even though the HTTP protocol is not full duplex. So based on my experience with some of the boost beast http read functions, those functions (mentioned below) are designed to follow the HTTP protocol which is half duplex while my application uses a modified HTTP protocol which is full duplex.  
  
Currently, I am able to call `boost::beast::http::read(*stream_,read_buffer_,res);` (blocking call) in a separate thread to read all the processed chunks all at once after receiving the `HTTP/1.1 200 OK` header from the server (half duplex). Is there a method in the boost beast library to read the processed chunks directly from the socket? Otherwise, how can I get the socket descriptor from `boost::asio::ssl::stream<tcp::socket>* stream_;` to read the socket?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-25 23:45:07 UTC  
> Updated at: 2022-08-25 23:45:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2509#issuecomment-1227861843  

First of all if you want full duplex you need to use asynchronous calls. Otherwise you will be accessing the socket concurrently, which results in undefined behavior. Second, you should use an overload of `http::async_read_some` which uses a `parser`, then you can have complete control over receiving the chunks:  
  
https://www.boost.org/doc/libs/1_80_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_some.html

---

## Comment 2

> Username: PasinduKollure  
> Created at: 2022-08-26 21:58:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2509#issuecomment-1228996576  

Thank you for the information on the `parser`! I am close to testing out my custom `parser` class but is there an asynchronous write that I can use to replace [boost::beast::net::write()](https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_http/chunked_encoding.html#:~:text=emit%20three%20chunks%3A-,net%3A%3Awrite(sock%2C%20make_chunk(get_next_chunk_body()))%3B,-net%3A%3Awrite)? I followed [chunked encoding example](https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_http/chunked_encoding.html) and I am using the `boost::beast::net::write()` function which requires `chunk_body` type as an argument for passing the message as seen [here](https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_http/chunked_encoding.html#:~:text=emit%20three%20chunks%3A-,net%3A%3Awrite(sock%2C%20make_chunk(get_next_chunk_body()))%3B,-net%3A%3Awrite).

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-26 22:18:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2509#issuecomment-1229007718  

Did you mean `beast::http::async_write`?

---

## Comment 4

> Username: PasinduKollure  
> Created at: 2022-08-26 22:48:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2509#issuecomment-1229022729  

Does calling an `async_write` close the http session after it writes the message? The program I am creating is intended to send multiple chunks of data in one HTTP session and I was wondering if `async_write` will close the HTTP connection or do I have control over ending the transmission of chunks by writing "0\r\n" to the stream using `async_write()`?

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2022-08-30 09:30:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2509#issuecomment-1231410345  

`async_write` does not close the connection. You can either send the end of the chunk manually or you can use `make_chunk_last` which is recommended.

---

## Comment 6

> Username: PasinduKollure  
> Created at: 2022-09-02 18:42:27 UTC  
> Updated at: 2022-09-02 18:42:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2509#issuecomment-1235803518  

I managed to use [async_read](https://www.boost.org/doc/libs/1_80_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read/overload1.html) with a custom `parser` which is derived from [basic_parser](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__basic_parser.html).   
  
One thread calls [async_header_write](https://www.boost.org/doc/libs/1_80_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_write_header.html), and [async_write](https://www.boost.org/doc/libs/1_80_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_write/overload2.html) to write chunks (used `boost::beast::http::make_chunk()` to make chunks) while the second thread calls `async_read` with the custom `parser` to read the incoming chunks.  
Thanks for the help!
