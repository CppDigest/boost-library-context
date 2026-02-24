# #63 - ASIO always requires threading to be enabled [Closed]

> Username: djarek  
> Created at: 2017-11-12 19:48:10 UTC  
> Updated at: 2017-12-02 08:19:46 UTC  
> Closed at: 2017-12-02 08:19:46 UTC  
> Url: https://github.com/boostorg/asio/issues/63  

```cpp  
#include <boost/asio/io_context.hpp>  
  
int main()  
{  
    boost::asio::io_context srv;  
    srv.post([](){});  
    srv.run();  
}  
```  
Requires -pthread compiler switch. The equivalent snippet compiled just fine without threading before 1.66.  
I believe this is caused by the associated_executor<> trait having system_executor as the fallback executor.

---

## Comment 1

> Username: chriskohlhoff  
> Created at: 2017-12-02 08:19:46 UTC  
> Url: https://github.com/boostorg/asio/issues/63#issuecomment-348676865  

Use `BOOST_ASIO_DISABLE_THREADS` if you want to disable thread support.  
  
Technically, asio has always required pthread support. It "worked" in the past because glibc provides stubs for some pthread functions as weak symbols.
