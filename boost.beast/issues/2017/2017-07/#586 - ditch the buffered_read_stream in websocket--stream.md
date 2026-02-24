# #586 - ditch the buffered_read_stream in websocket::stream [Closed]

> Username: vinniefalco  
> Created at: 2017-07-04 01:57:07 UTC  
> Updated at: 2017-08-06 22:40:12 UTC  
> Closed at: 2017-08-06 22:40:12 UTC  
> Url: https://github.com/boostorg/beast/issues/586  

The asynchronous operations add overhead and we could probably get a performance boost. The performance needs to be measured though. The way to get rid of it is to just have some helper functions such as  
  
```  
namespace detail {  
  
// Asynchronous read, look at InBuffer first ,copies whats there and returns true if not empty  
// Otherwise performs a real asynchronous read. Only works with callbacks, no future / coro  
// and thus not a public interface.  
//  
template<class AsyncReadStream, class OutBuffer, class InBuffer>  
bool  
async_buffered_read(AsyncreadStream&, OutBuffer&, InBuffer&);  
  
} // detail  
```  
  
This eliminates a useless io_service dispatch on **every websocket read**.  
  
We might consider removing `buffered_read_stream` from the public interfaces.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-04 23:05:53 UTC  
> Url: https://github.com/boostorg/beast/issues/586#issuecomment-320371576  

This is done, and 3x faster now :)
