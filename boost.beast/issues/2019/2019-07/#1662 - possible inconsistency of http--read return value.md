# #1662 - possible inconsistency of http::read return value [Closed]

> Username: romange  
> Created at: 2019-07-19 11:50:17 UTC  
> Updated at: 2024-02-14 10:48:02 UTC  
> Closed at: 2024-02-14 10:48:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1662  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
### Version of Beast  
248  
  
### Steps necessary to reproduce the problem  
```  
  auto& body = parser.get().body();  
  body.data = dest_ptr;  
  auto& left_available = body.size;  
  left_available = dest_capacity;  
  
  error_code ec;  
  size_t sz_read = http::read(*client_, tmp_buffer_, parser, ec);  
  if (ec) {...}  
  CHECK_EQ(sz_read, dest_capacity - left_available);  
```  
Fails at CHECK_EQ `Check failed: sz_read == dest_capacity - left_available (65599 vs. 65536)`

---

## Comment 1

> Username: djarek  
> Created at: 2019-07-19 12:00:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1662#issuecomment-513203040  

`http::read` reports the number of bytes read from the lower layer, so it will include headers or chunk encoding.

---

## Comment 2

> Username: romange  
> Created at: 2019-07-19 12:11:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1662#issuecomment-513205953  

Ok, not a bug then. In any case, maybe it's worth extending the remark here since it's not immediately clear.  
https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__read/overload2.html

---

## Comment 3

> Username: ashtum  
> Created at: 2024-02-14 10:48:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1662#issuecomment-1943510913  

https://github.com/boostorg/beast/commit/105466172304ee27f6201725fbaa78e478008415 changed the interpretation of the returned bytes_transferred value in HTTP read operations so it represent the number of bytes consumed by the parser.  
Documented in https://github.com/boostorg/beast/pull/2816.
