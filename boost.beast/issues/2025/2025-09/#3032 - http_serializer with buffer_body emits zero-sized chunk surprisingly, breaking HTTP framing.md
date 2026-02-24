# #3032 - http_serializer with buffer_body emits zero-sized chunk surprisingly, breaking HTTP framing [Closed]

> Username: pgit  
> Created at: 2025-09-17 14:34:40 UTC  
> Updated at: 2025-10-10 11:59:52 UTC  
> Closed at: 2025-10-10 11:59:52 UTC  
> Url: https://github.com/boostorg/beast/issues/3032  

When finishing a chunked body like this:  
````c++  
message.body().data = <non-null-ptr>  
message.body().size = 0;  
message.body().more = false;  
````  
TWO zero-sized chunks are emitted, breaking HTTP framing. Only if `data` is actually set to `nullptr`, this works.  
  
My usecase is serializing a sequence of `std::string_view`, using an empty one as EOM marker. An empty string view may have a non-null data pointer. Only if I do  
```c++  
message.body().data = sv.size() ? const_cast<void*>(sv.data()) : nullptr;  
message.body().size = sv.size();  
message.body().more = sv.size() != 0; // empty sv --> EOM  
```  
this becomes safe. One could argue that beast is so low-level that the caller has to know about this subtle difference, but as the 0-sized chunk has a very specific meaning in HTTP, IMO a safeguard against this could be warranted.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-09-17 16:06:25 UTC  
> Url: https://github.com/boostorg/beast/issues/3032#issuecomment-3303674801  

if `size==0` then beast should ignore `data`

---

## Comment 2

> Username: pgit  
> Created at: 2025-09-17 19:42:27 UTC  
> Updated at: 2025-09-17 20:13:10 UTC  
> Url: https://github.com/boostorg/beast/issues/3032#issuecomment-3304341968  

~[Godbolt: Working example](https://godbolt.org/z/7nz7bd3WW)~ [Simpler, sync version](https://godbolt.org/z/WaonxPoYr)  
  
```c++  
   using Serializer = http::response_serializer<http::buffer_body>;  
   using Message = std::remove_const_t<typename Serializer::value_type>;  
   Message message;  
   Serializer serializer{message};  
  
   message.chunked(true);  
  
   error_code ec;  
   auto text = asio::buffer("Hello, World!"sv);  
   message.body().data = const_cast<void*>(text.data());  
   message.body().size = text.size();  
   message.body().more = true;  
   http::write(stream, serializer, ec);  
   assert(ec == http::error::need_buffer);  
  
   if (with_nullptr)  
      message.body().data = nullptr;  
   message.body().size = 0;  
   message.body().more = false;  
   http::write(stream, serializer, ec);  
   assert(!ec);  
```  
  
with_nullptr = true  
```  
HTTP/1.1 200 OK  
Transfer-Encoding: chunked  
  
d  
Hello, World!  
0  
  
```  
  
with_nullptr = false  
```  
HTTP/1.1 200 OK  
Transfer-Encoding: chunked  
  
d  
Hello, World!  
0  
  
0  
  
```
