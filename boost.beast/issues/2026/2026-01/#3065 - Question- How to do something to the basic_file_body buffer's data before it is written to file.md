# #3065 - Question: How to do something to the basic_file_body buffer's data before it is written to file? [Closed]

> Username: albxy  
> Created at: 2026-01-05 11:33:51 UTC  
> Updated at: 2026-01-07 10:32:59 UTC  
> Closed at: 2026-01-07 10:27:27 UTC  
> Url: https://github.com/boostorg/beast/issues/3065  

I've been thinking how to implement file upload functions.I know there are ways like [https://www.boost.org/doc/libs/latest/libs/beast/doc/html/beast/using_http/parser_stream_operations/incremental_read.html](url) for sync operations.  
But I want to deal with it more elegantly,as the title shows.  
Previous BodyReader requires ConstBuffer,so I can't change the data of the buffers in the put() function.Do we have a way to change the buffer data before it is written to file efficiently(instead of copying it)?  
I really would like to know.Thanks.

---

## Comment 1

> Username: ashtum  
> Created at: 2026-01-05 15:45:48 UTC  
> Url: https://github.com/boostorg/beast/issues/3065#issuecomment-3710967079  

> Do we have a way to change the buffer data before it is written to file efficiently(instead of copying it)?  
  
[http::file_body](https://www.boost.org/doc/libs/latest/libs/beast/doc/html/beast/ref/boost__beast__http__file_body.html) doesn't allow modification before writing. however, you can use [http::buffer_body](https://www.boost.org/doc/libs/latest/libs/beast/doc/html/beast/ref/boost__beast__http__buffer_body.html) and open a file manually (using [beast::file](https://www.boost.org/doc/libs/latest/libs/beast/doc/html/beast/ref/boost__beast__file.html)). There's a synchronous example that demonstrates the interface: https://www.boost.org/doc/libs/latest/libs/beast/doc/html/beast/more_examples/http_relay.html.  
  
You can also create your own body type by copying `http::file_body` and modifying it to suit your needs.

---

## Comment 2

> Username: albxy  
> Created at: 2026-01-06 05:27:34 UTC  
> Url: https://github.com/boostorg/beast/issues/3065#issuecomment-3713166975  

Thanks.Now I realized it is a const buffer.Then I took a look at the buffer_body,finding it using net::buffre_copy,which might be sufficient in pracice?Do we have better ways(in asio&beast) in theory to deal with this kind of problems?

---

## Comment 3

> Username: ashtum  
> Created at: 2026-01-06 06:00:59 UTC  
> Url: https://github.com/boostorg/beast/issues/3065#issuecomment-3713236890  

> Thanks.Now I realized it is a const buffer.  
  
Are you referring to [http::buffer_body::value_type::data](https://www.boost.org/doc/libs/latest/libs/beast/doc/html/beast/ref/boost__beast__http__buffer_body__value_type/data.html)? It's a `void*`.  
  
> finding it using net::buffre_copy,which might be sufficient in pracice?  
  
Sorry, I didn’t quite understand that part.

---

## Comment 4

> Username: albxy  
> Created at: 2026-01-06 09:11:22 UTC  
> Url: https://github.com/boostorg/beast/issues/3065#issuecomment-3713824269  

> Are you referring to [http::buffer_body::value_type::data](https://www.boost.org/doc/libs/latest/libs/beast/doc/html/beast/ref/boost__beast__http__buffer_body__value_type/data.html)? It's a void*.  
  
Nope,I mean the BodyReader's requirement   
> b is an object whose type meets the requirements of [ConstBufferSequence](https://www.boost.org/doc/libs/latest/doc/html/boost_asio/reference/ConstBufferSequence.html)  
  
> Sorry, I didn’t quite understand that part.  
  
https://github.com/boostorg/beast/blob/67106ebaab7a644a9bdb5d6e6b0a3f4ad1d0e4f7/include/boost/beast/http/buffer_body.hpp#L133-L134  
which might be costly?

---

## Comment 5

> Username: ashtum  
> Created at: 2026-01-06 12:42:25 UTC  
> Url: https://github.com/boostorg/beast/issues/3065#issuecomment-3714564833  

> Lines 133 to 134 in [67106eb](/boostorg/beast/commit/67106ebaab7a644a9bdb5d6e6b0a3f4ad1d0e4f7)  
>   
>  net::buffer_copy(net::buffer(   
>      body_.data, body_.size), buffers);   
>   
> which might be costly?  
  
Yes, this copy is unavoidable in this model. However, given that memcpy is relatively inexpensive compared to parsing, it does not pose an issue for small and medium-sized bodies.  
  
If the HTTP message includes a `Content-Length` field, you can limit the parser to reading only the headers and then read the body directly from the connection. However, I wouldn’t recommend this approach without first performing some measurements, especially if you are dealing with a TLS connection.

---

## Comment 6

> Username: albxy  
> Created at: 2026-01-06 12:58:15 UTC  
> Url: https://github.com/boostorg/beast/issues/3065#issuecomment-3714608667  

> Yes, this copy is unavoidable in this model. However, given that memcpy is relatively inexpensive compared to parsing, it does not pose an issue for small and medium-sized bodies.  
  
I have completely understood.  
  
> If the HTTP message includes a Content-Length field, you can limit the parser to reading only the headers and then read the body directly from the connection. However, I wouldn’t recommend this approach without first performing some measurements, especially if you are dealing with a TLS connection.  
  
What does this refer to?

---

## Comment 7

> Username: ashtum  
> Created at: 2026-01-06 14:27:52 UTC  
> Url: https://github.com/boostorg/beast/issues/3065#issuecomment-3714913237  

> > If the HTTP message includes a Content-Length field, you can limit the parser to reading only the headers and then read the body directly from the connection. However, I wouldn’t recommend this approach without first performing some measurements, especially if you are dealing with a TLS connection.  
>   
> What does this refer to?  
  
You can use [http::async_read_header](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html) to read the header portion of the message, then query [http::message::payload_size](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__message/payload_size.html) to determine how much data still needs to be read from the stream. This approach allows you to avoid an extra `memcpy`. Note that a small portion of the body data may already reside in the dynamic buffer passed to `http::async_read_header`, so you will need to `memcpy` that part first.

---

## Comment 8

> Username: albxy  
> Created at: 2026-01-07 03:32:44 UTC  
> Url: https://github.com/boostorg/beast/issues/3065#issuecomment-3717187162  

I get it.Well,is it theoretically possible to realize   
  
> use [http::async_read_header](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html) to read the header portion of the message, then query [http::message::payload_size](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__message/payload_size.html) to determine how much data still needs to be read from the stream.  
  
in all previous bodies? Though optimizing it in practice would be a large work with small effect?

---

## Comment 9

> Username: ashtum  
> Created at: 2026-01-07 09:59:00 UTC  
> Url: https://github.com/boostorg/beast/issues/3065#issuecomment-3718110003  

> in all previous bodies?  
  
You can use [http::empty_body](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__empty_body.html).  
  
> Though optimizing it in practice would be a large work with small effect?  
  
Correct, I don’t expect to see a significant improvement, especially if the connection is encrypted.
