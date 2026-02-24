# #2794 - Discarding request body without needing a temporary buffer [Closed]

> Username: ecorm  
> Created at: 2024-01-10 16:39:43 UTC  
> Updated at: 2024-01-12 13:53:16 UTC  
> Closed at: 2024-01-10 17:12:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2794  

How can I efficiently discard a request body without needing to use a temporary `string_body` or `vector_body`?  
  
My use case is an HTTP REST server using a configurable map of request-target strings to handler functions. When a request-target is not found in the map, the server should flush the request body and send a 404 response back to the client, while maintaining the persistent (Keep-Alive) HTTP connection. I can flush the request body easily enough via a `string_body` or `vector_body`, but I was wondering how to flush the body bytes without dynamically allocating memory, or using a dedicated static "flush buffer".  
  
I thought of using `empty_body`, but the documentation says:  
  
> ... the parser will fail with the error `http::unexpected_body`  
  
Is my best bet to write a custom Body class for this purpose?

---

## Comment 1

> Username: ecorm  
> Created at: 2024-01-10 16:52:53 UTC  
> Updated at: 2024-01-10 16:53:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2794#issuecomment-1885229844  

I just realized after asking that I'm using a `http::request_parser<http::string_body>` to perform the initial `http::async_read_header`. I probably can't just use a different `http::request_parser<flush_body>` to read the remainder of the request because there's presumably no way to transfer the parsing state from one parser to another.  
  
I'm also (willingly) incurring the cost of dynamic allocations via `http::request_parser<http::string_body>` for legitimate request-targets anyway. There's probably no point in avoiding that cost when the request-target is not found.

---

## Comment 2

> Username: ashtum  
> Created at: 2024-01-10 16:53:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2794#issuecomment-1885230869  

You can utilize a [http::buffer_body](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__http__buffer_body.html) that points to a static buffer for that purpose, these examples might help:  
https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/more_examples/http_relay.html  
https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/using_http/parser_stream_operations/incremental_read.html

---

## Comment 3

> Username: ecorm  
> Created at: 2024-01-10 17:04:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2794#issuecomment-1885249947  

> You can utilize a `http::buffer_body`  
  
Yes, I know. I just have to decide if it's better to use one large (1MiB by default) `http::buffer_body` per client session, or use a `string_body` with the assumption that most request bodies will be small. The server is not intended to be used in a large-scale manner, and I don't have much time to perform stress tests, so I have to pick a reasonable approach and live with it.

---

## Comment 4

> Username: ecorm  
> Created at: 2024-01-10 17:12:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2794#issuecomment-1885262631  

> I just have to decide if it's better to use one large (1MiB by default) `http::buffer_body` per client session  
  
No, that's not scalable at all, even at the smallish scale I intent to use the server on a Raspberry Pi-like device. Probably better to use `string_body`, watch for `bad_alloc` failures, and do graceful degradation when needed.

---

## Comment 5

> Username: ecorm  
> Created at: 2024-01-11 19:24:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2794#issuecomment-1887816539  

I've studied the incremental reading example more closely, and I think I understand things better now. I think what I need is a `parser<basic_dynamic_body<static_buffer>>`, which I can use to incrementally append data to a `std::string` to obtain a contiguous request body. In my use case where the request-target is not found, I still do the incremental reading, but simply ignore the intermediate data.

---

## Comment 6

> Username: ecorm  
> Created at: 2024-01-12 13:52:20 UTC  
> Updated at: 2024-01-12 13:53:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2794#issuecomment-1889264261  

Just a quick note that I ended up using the [incremental read example](https://www.boost.org/doc/libs/release/libs/beast/doc/html/beast/using_http/parser_stream_operations/incremental_read.html), except that I use an adjustably-sized `std::string` for the `buf` object so that its size can be tuned without recompiling. It seems to work. The `sizeof(buf) - p.get().body().size` arithmetic seems "magical" to me and I don't understand how that works, but it does seem to work.  
  
If the request-target is valid, I can incrementally append the `buf` to a contiguous `std::string` for the request body. This approach has the minor disadvantage that I need to pass separate `request_header` and `std::string` (body) arguments to the API endpoint handler, instead of a single unified `request_message`.
