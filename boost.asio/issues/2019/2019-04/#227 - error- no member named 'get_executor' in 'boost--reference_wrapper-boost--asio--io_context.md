# #227 - error: no member named 'get_executor' in 'boost::reference_wrapper<boost::asio::io_context> [Closed]

> Username: Hardy5012  
> Created at: 2019-04-25 01:56:49 UTC  
> Updated at: 2019-04-29 08:17:01 UTC  
> Closed at: 2019-04-29 08:17:01 UTC  
> Url: https://github.com/boostorg/asio/issues/227  

I want post io_context reference in handle function.  
  
`   
boost::asio::steady_timer t(boost::ref(io), boost::asio::chrono::seconds(5));  
`  
  
before boost 1.70.0 it works fine  
on boost 1.70.0  
compiler reports:  
  
> In file included from test_asio.cpp:2:  
In file included from /usr/local/boost_1.70/include/boost/asio.hpp:24:  
In file included from /usr/local/boost_1.70/include/boost/asio/basic_datagram_socket.hpp:20:  
In file included from /usr/local/boost_1.70/include/boost/asio/basic_socket.hpp:21:  
/usr/local/boost_1.70/include/boost/asio/detail/io_object_impl.hpp:87:40: error: no member named 'get_executor' in 'boost::reference_wrapper<boost::asio::io_context>'  
      implementation_executor_(context.get_executor(),  
                               ~~~~~~~ ^  
/usr/local/boost_1.70/include/boost/asio/basic_waitable_timer.hpp:272:7: note: in instantiation of function template specialization  
      'boost::asio::detail::io_object_impl<boost::asio::detail::deadline_timer_service<boost::asio::detail::chrono_time_traits<std::chrono::_V2::steady_clock, boost::asio::wait_traits<std::chrono::_V2::steady_clock> > >,  
      boost::asio::executor>::io_object_impl<const boost::reference_wrapper<boost::asio::io_context> >' requested here  
    : impl_(context)  
      ^  
test_asio.cpp:7:27: note: in instantiation of function template specialization 'boost::asio::basic_waitable_timer<std::chrono::_V2::steady_clock, boost::asio::wait_traits<std::chrono::_V2::steady_clock>, boost::asio::executor>::basic_waitable_timer<const  
      boost::reference_wrapper<boost::asio::io_context> >' requested here  
boost::asio::steady_timer t(boost::ref(io), boost::asio::chrono::seconds(5));

---

## Comment 1

> Username: djarek  
> Created at: 2019-04-25 16:50:26 UTC  
> Url: https://github.com/boostorg/asio/issues/227#issuecomment-486751866  

@Hardy5012 the constructor that this code used in 1.69 no longer exists (the one that accepts non-template `io_context&`). If you drop the `boost::ref()` it should work both in 1.69 and 1.70.

---

## Comment 2

> Username: Hardy5012  
> Created at: 2019-04-26 09:17:29 UTC  
> Url: https://github.com/boostorg/asio/issues/227#issuecomment-486988692  

@djarek If I want use `std::bind` pass io_context reference, not value copy.How can I do

---

## Comment 3

> Username: djarek  
> Created at: 2019-04-26 09:40:05 UTC  
> Url: https://github.com/boostorg/asio/issues/227#issuecomment-486996548  

```  
asio::post(ioc, [&ioc](){ asio::steady_timer t(ioc,std::chrono::seconds(5)) };  
```
