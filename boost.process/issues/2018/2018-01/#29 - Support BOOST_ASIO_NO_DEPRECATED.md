# #29 - Support BOOST_ASIO_NO_DEPRECATED [Closed]

> Username: hia3  
> Created at: 2018-01-19 05:15:28 UTC  
> Updated at: 2019-04-09 06:34:18 UTC  
> Closed at: 2019-04-09 06:34:18 UTC  
> Url: https://github.com/boostorg/process/issues/29  

#define BOOST_ASIO_NO_DEPRECATED 1  
    #include <boost/process.hpp>  
    int main() {}  
  
Error:  
  
    boost/process/detail/windows/async_pipe.hpp:102:39: error: no member named 'get_io_context' in 'boost::asio::windows::stream_handle'  
            _sink = handle_type(_sink.get_io_context());

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-02-05 22:30:20 UTC  
> Url: https://github.com/boostorg/process/issues/29#issuecomment-363243589  

It might be a good idea to support that, but - I'm sorry to say -  I won't get around to implement that anytime soon. I'd accept PRs though

---

## Comment 2

> Username: krojew  
> Created at: 2018-04-12 07:00:49 UTC  
> Url: https://github.com/boostorg/process/issues/29#issuecomment-380699503  

This would be a good addition, since boost::process now fails to build whenever this define is used.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2019-04-09 06:34:18 UTC  
> Url: https://github.com/boostorg/process/issues/29#issuecomment-481121248  

Fixed, will publish soon.
