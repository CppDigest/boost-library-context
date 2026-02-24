# #284 - posix::basic_stream_descriptor<custom_executor> is not movable [Closed]

> Username: Marc-Aldorasi-Imprivata  
> Created at: 2019-09-11 17:04:56 UTC  
> Updated at: 2020-12-30 01:09:28 UTC  
> Closed at: 2020-12-30 01:09:27 UTC  
> Url: https://github.com/boostorg/asio/issues/284  

When compiling this program  
  
```c++  
#include <boost/asio.hpp>  
  
int main() {  
	boost::asio::io_context ctx_;  
	boost::asio::posix::basic_stream_descriptor<boost::asio::io_context::executor_type> desc(ctx_.get_executor());  
	auto desc2 = std::move(desc);  
}  
```  
  
I get the following error:  
  
```  
/boost/boost/asio/posix/basic_stream_descriptor.hpp: In instantiation of ‘boost::asio::posix::basic_stream_descriptor<Executor>::basic_stream_descriptor(boost::asio::posix::basic_stream_descriptor<Executor>&&) [with Executor = boost::asio::io_context::executor_type]’:  
x.cpp:6:29:   required from here  
/boost/boost/asio/posix/basic_stream_descriptor.hpp:141:34: error: type ‘boost::asio::posix::basic_descriptor<>’ is not a direct base of ‘boost::asio::posix::basic_stream_descriptor<boost::asio::io_context::executor_type>’  
  141 |     : descriptor(std::move(other))  
      |                                  ^  
```  
  
It looks like `basic_stream_descriptor` uses `descriptor` to refer to its base class, but that is incorrect when using a non-default executor.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:09:26 UTC  
> Url: https://github.com/boostorg/asio/issues/284#issuecomment-752292931  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#719](https://github.com/chriskohlhoff/asio/issues/719).
