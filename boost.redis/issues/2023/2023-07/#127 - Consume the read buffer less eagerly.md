# #127 - Consume the read buffer less eagerly [Closed]

> Username: mzimbres  
> Created at: 2023-07-29 18:24:08 UTC  
> Updated at: 2023-08-31 10:54:59 UTC  
> Closed at: 2023-08-31 10:54:58 UTC  
> Url: https://github.com/boostorg/redis/issues/127  

In recent benchmarks it was shown that rotating the buffer less eagerly results in big performance improvements. However we can do better. At the moment we rotate the buffer every time a complete RESP3 message has been read. For example, if the server sends  
  
```  
WWWWWWWWWW XXXXXXXXXX YYYYYYYYYYY ZZZZZZZZZZZ  
```  
  
The buffer will be rotate as follows  
  
```  
XXXXXXXXXX YYYYYYYYYYY ZZZZZZZZZZZ  
YYYYYYYYYY ZZZZZZZZZZZ  
ZZZZZZZZZZZ  
...  
```  
  
The C++ code that reads three RESP3 messages looks like this right now  
  
```cpp  
std::size_t consumed = 0;  
auto dbuf = net::dynamic_buffer(buffer);  
consumed = co_await async_read(socket, dbuf);  
dbuf.consume(consumed);  
consumed = co_await async_read(socket, dbuf, adapt2(resp));  
dbuf.consume(consumed);  
consumed = co_await async_read(socket, dbuf);  
dbuf.consume(consumed);  
```  
  
The current dynamic buffer v2 concept does not allow us specifying this offset, so it would have to be implemented in the parser. Its constructor should receive an offset parameter and a function to reset it to zero to be called when the caller decides to rotate. The c++ code would then look like  
  
```cpp  
std::size_t consumed = 0;  
auto dbuf = net::dynamic_buffer(buffer);  
consumed += co_await async_read(socket, dbuf, adapter, consumed);  
consumed += co_await async_read(socket, dbuf, adapter, consumed));  
consumed += co_await async_read(socket, dbuf, adapter, consumed));  
  
// Rotates the buffer at once.  
dbuf.consume(consumed);  
```  
  
With these changes we would be able to read large aggregate data types without incurring any rotation. How much memory the user is willing to accumulate should be a parameter in the connection class.

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-08-05 21:07:55 UTC  
> Url: https://github.com/boostorg/redis/issues/127#issuecomment-1666606813  

After thinking a bit about this problem I have come to the conclusion that it would be simpler to create a new dynamic buffer class that accumulates some data before erasing it. This is better than having the feature built in the parser, making it more complex.

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-08-31 10:54:58 UTC  
> Url: https://github.com/boostorg/redis/issues/127#issuecomment-1700818726  

After performing many benchmarks, avoiding buffer rotation is no even measurable. This is not something I will pursue again anytime soon.
