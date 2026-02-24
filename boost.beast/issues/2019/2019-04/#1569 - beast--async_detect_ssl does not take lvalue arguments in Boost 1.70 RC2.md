# #1569 - beast::async_detect_ssl does not take lvalue arguments in Boost 1.70 RC2 [Closed]

> Username: KABoissonneault  
> Created at: 2019-04-12 14:14:04 UTC  
> Updated at: 2019-04-20 19:05:08 UTC  
> Closed at: 2019-04-20 19:05:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1569  

Someone posted this snippet on Slack, saying it doesn't compile  
  
```  
#include <boost/beast/core.hpp>  
  
using boost::asio::ip::tcp;  
namespace beast = boost::beast;  
void good(tcp::socket & socket, beast::flat_buffer & buf)  
{  
    beast::async_detect_ssl(socket, buf, [](beast::error_code ec, bool uses_ssl){});  
}  
  
void bad(tcp::socket & socket, beast::flat_buffer & buf)  
{  
      
    auto h = [](beast::error_code ec, bool uses_ssl){};  
    beast::async_detect_ssl(socket, buf, h);  
        /* error: beast/core/detect_ssl.hpp:513:25: error: type ‘boost::beast::async_base<<lambda(boost::beast::error_code, bool)>&, boost::asio::executor, std::allocator<void> >’ is not a direct base of ‘boost::beast::detail::detect_ssl_op<<lambda(boost::beast::error_code, bool)>, boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::beast::basic_flat_buffer<std::allocator<char> > >’  
        */  
}  
```  
  
I suspect this is because of the constructor of `detect_ssl_op`  
```  
template<class DetectHandler_>  
    detect_ssl_op(  
        DetectHandler_&& handler,  
        AsyncReadStream& stream,  
        DynamicBuffer& buffer)  
        : beast::async_base<  
            DetectHandler_,  
            beast::executor_type<AsyncReadStream>>(  
                std::forward<DetectHandler_>(handler),  
                stream.get_executor())  
        , stream_(stream)  
        , buffer_(buffer)  
```  
`async_base` here is instantiated with `DetectHandler_`, which is not decayed, while the base class uses the decayed handler
