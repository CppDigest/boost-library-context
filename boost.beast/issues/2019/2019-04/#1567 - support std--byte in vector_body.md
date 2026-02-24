# #1567 - support std::byte in vector_body [Closed]

> Username: maddanio  
> Created at: 2019-04-11 10:02:20 UTC  
> Updated at: 2019-04-20 19:05:08 UTC  
> Closed at: 2019-04-20 19:05:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1567  

### Version of Beast  
  
248  
  
### Steps necessary to reproduce the problem  
  
try to construct a `http::response<http::vector_body<std::byte>>`  
  
### All relevant compiler information  
  
Apple LLVM version 10.0.1 (clang-1001.0.46.3)  
Target: x86_64-apple-darwin18.5.0  
Thread model: posix  
  
  
It would be kinda nice not to have to use uint8_t or similar for receiving raw binary data.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-19 01:23:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1567#issuecomment-484738746  

Well what is the error?
