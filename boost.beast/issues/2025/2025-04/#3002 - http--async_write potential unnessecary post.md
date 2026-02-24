# #3002 - http::async_write potential unnessecary post [Closed]

> Username: jakob-stark-viavisolutions  
> Created at: 2025-04-24 07:46:33 UTC  
> Updated at: 2025-04-27 12:38:07 UTC  
> Closed at: 2025-04-27 12:38:07 UTC  
> Url: https://github.com/boostorg/beast/issues/3002  

Consider the follwing small test program (godbolt.com link at the end):  
  
```cpp  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
  
using namespace boost::asio;  
using namespace boost::beast;  
  
awaitable<void> run() {  
    auto executor = co_await this_coro::executor;  
  
    readable_pipe read_end(executor);  
    writable_pipe write_end(executor);  
    connect_pipe(read_end, write_end);  
  
    http::request<http::empty_body> req;  
    co_await http::async_write(write_end, req);  
  
    std::string msg{"a"};  
    co_await async_write(write_end, buffer(msg));  
}  
  
int main() {  
    thread_pool ioc{1};  
    co_spawn(ioc, run(), detached);  
    ioc.join();  
}  
```  
  
When compiled with `BOOST_ASIO_ENABLE_HANDLER_TRACKING` (gcc 14.2, boost 1.87.0) it produces the following output:  
  
```  
@asio|1745479100.897508|0^1|in 'co_spawn_entry_point' (/app/boost/include/boost/asio/impl/co_spawn.hpp:197)  
@asio|1745479100.897508|0*1|thread_pool@0x7ffc0aed0f30.execute(blk=never,rel=fork)  
@asio|1745479100.897725|>1|  
@asio|1745479100.897999|1^2|in 'http::async_write_some' (/app/boost/include/boost/beast/http/impl/write.hpp:64)  
@asio|1745479100.897999|1^2|called from 'http::async_write' (/app/boost/include/boost/beast/http/impl/write.hpp:232)  
@asio|1745479100.897999|1^2|called from 'http::async_write(msg)' (/app/boost/include/boost/beast/http/impl/write.hpp:290)  
@asio|1745479100.897999|1^2|called from 'run' (/app/example.cpp:15)  
@asio|1745479100.897999|1*2|descriptor@0x2029d708.async_write_some  
@asio|1745479100.898032|.2|non_blocking_write,ec=system:0,bytes_transferred=13  
@asio|1745479100.898040|>2|ec=system:0,bytes_transferred=13  
@asio|1745479100.898048|2^3|in 'http::async_write_some' (/app/boost/include/boost/beast/http/impl/write.hpp:64)  
@asio|1745479100.898048|2^3|called from 'http::async_write' (/app/boost/include/boost/beast/http/impl/write.hpp:232)  
@asio|1745479100.898048|2^3|called from 'http::async_write(msg)' (/app/boost/include/boost/beast/http/impl/write.hpp:290)  
@asio|1745479100.898048|2^3|called from 'run' (/app/example.cpp:15)  
@asio|1745479100.898048|2*3|thread_pool@0x7ffc0aed0f30.execute(blk=never,rel=fork)  
@asio|1745479100.898062|<2|  
@asio|1745479100.898067|<1|  
@asio|1745479100.898075|>3|  
@asio|1745479100.898093|3^4|in 'async_write' (/app/boost/include/boost/asio/impl/write.hpp:362)  
@asio|1745479100.898093|3^4|called from 'run' (/app/example.cpp:18)  
@asio|1745479100.898093|3*4|descriptor@0x2029d708.async_write_some  
@asio|1745479100.898102|.4|non_blocking_write,ec=system:0,bytes_transferred=1  
@asio|1745479100.898107|<3|  
@asio|1745479100.898112|>4|ec=system:0,bytes_transferred=1  
@asio|1745479100.898117|4|descriptor@0x2029d708.close  
@asio|1745479100.898125|4|descriptor@0x2029d6b8.close  
@asio|1745479100.898137|<4|  
```  
  
I do not understand, why the `http::async_write` behaves differently from asio's `async_write` in terms of calling the continuation. The operation `>1` stays open during the whole `http::async_write`. It is only left after it scheduled operation `>3` with fork relationship, which is typically done via `asio::post` - however I cannot see any call to `net::post` in the http write implementation.  
  
Where is the origin of the `thread_pool.execute(blk=never,rel=fork)`, and is this an unnessecary perfomance issue? If yes, is there a workaround?  
  
The handler trace looks the same for different versions of boost, only in boost 1.83.0 the extra "post" disappears:  
  
 - boost 1.87.0 [godbolt](https://godbolt.org/z/coKrvEbK6)  
 - boost 1.86.0 [godbolt](https://godbolt.org/z/ssfMMcET9)  
 - boost 1.85.0 [godbolt](https://godbolt.org/z/ann4nohEK)  
 - boost 1.84.0 [godbolt](https://godbolt.org/z/EfcxqWcez)  
 - boost 1.83.0 [godbolt](https://godbolt.org/z/9j8cKPzv9)  << here the `thread_pool.execute` disappeared  
  
So maybe the issue is related to the support for immediate executors which was introduced in 1.84.0?

---

## Comment 1

> Username: ashtum  
> Created at: 2025-04-25 09:27:56 UTC  
> Updated at: 2025-04-25 12:14:47 UTC  
> Url: https://github.com/boostorg/beast/issues/3002#issuecomment-2829878881  

Great observation, thanks for pointing that out.  
  
I believe this issue stems from how `immediate_executor_type` is defined in `async_base`:    
https://github.com/boostorg/beast/blob/1d8dfae490125bd63efedfa7a3ecf20e8e9b54a5/include/boost/beast/core/async_base.hpp#L214-L223  
  
When no immediate executor is provided, it defaults to `asio::detail::default_immediate_executor`.    
As a result, Asio always assumes an immediate executor is present and attempts to execute the completion handler using it.  
  
This doesn't lead to additional post-operation, as we can see here: https://godbolt.org/z/vG5PfdP4r.    
However, it does result in unnecessary copies and allocations, which we need to address.
