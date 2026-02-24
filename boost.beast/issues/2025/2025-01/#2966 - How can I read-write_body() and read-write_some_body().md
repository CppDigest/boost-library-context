# #2966 - How can I read/write_body() and read/write_some_body()? [Closed]

> Username: feverzsj  
> Created at: 2025-01-01 17:29:00 UTC  
> Updated at: 2025-01-03 19:14:27 UTC  
> Closed at: 2025-01-03 19:14:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2966  

I see there is `read/write_header()`. But what about the body? I also want to read the body part by part using a smaller buffer (no need to form the full body in memory) no matter chunked or not.

---

## Comment 1

> Username: ashtum  
> Created at: 2025-01-01 17:43:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2966#issuecomment-2567091524  

You can use [http::async_read_some](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_some.html) for that.

---

## Comment 2

> Username: feverzsj  
> Created at: 2025-01-02 15:39:30 UTC  
> Updated at: 2025-01-02 15:39:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2966#issuecomment-2567966582  

> You can use [http::async_read_some](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_some.html) for that.  
  
So, I can just clear the body before `async_read_some()`? And set a new body part when `async_write_some()`.

---

## Comment 3

> Username: ashtum  
> Created at: 2025-01-02 15:45:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2966#issuecomment-2567976462  

if you want to reuse the buffer, you can consider using [http::buffer_body](https://www.boost.org/doc/libs/1_87_0/libs/beast/doc/html/beast/ref/boost__beast__http__buffer_body.html), here is an example demonstrating its usage: [HTTP Relay](https://www.boost.org/doc/libs/1_87_0/libs/beast/doc/html/beast/more_examples/http_relay.html).
