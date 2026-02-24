# #326 - Handle leak with calls to async_resolve with set_terminate_threads set [Closed]

> Username: Breakwell  
> Created at: 2020-02-26 15:25:26 UTC  
> Updated at: 2020-12-30 01:13:13 UTC  
> Closed at: 2020-12-30 01:13:12 UTC  
> Url: https://github.com/boostorg/asio/issues/326  

Executing the following code on Windows I am getting handle leaks:  
  
```  
#include <iostream>  
#include <boost/asio.hpp>  
  
namespace boost_system = boost::system;  
namespace boost_asio = boost::asio;  
  
class Client  
{  
public:  
  
    Client() :  
        r_(ioc_), sock_(ioc_)  
    {  
        boost::asio::detail::win_thread::set_terminate_threads(true);  
    }  
  
    ~Client()  
    {  
    }  
  
    void connectHandler(const boost::system::error_code& ec,  
        const boost_asio::ip::tcp::endpoint&) {  
    }  
  
    void resolveHandler(  
        const boost::system::error_code& ec,  
        boost_asio::ip::tcp::resolver::results_type results) {  
        if (!ec) {  
            boost::asio::async_connect(sock_,  
                results,  
                std::bind(&Client::connectHandler,  
                    this,  
                    std::placeholders::_1,  
                    std::placeholders::_2));  
        }  
        else  
        {  
            std::cout << ec.message();  
        }  
    }  
  
    void Connect()  
    {  
        r_.async_resolve(boost_asio::ip::tcp::resolver::query{ "kinesis.us-west-2.amazonaws.com", "443" },  
            std::bind(&Client::resolveHandler,  
                this,  
                std::placeholders::_1,  
                std::placeholders::_2));  
  
        {  
            boost_system::error_code rc;  
            ioc_.run(rc);  
            ioc_.reset();  
            if (rc) {  
                std::cout << "FAILURE: " << rc;  
            }  
        }  
  
        boost_asio::socket_base::keep_alive option(true);  
        sock_.set_option(option);  
    }  
  
private:  
    boost_asio::io_context ioc_;  
    boost_asio::ip::tcp::resolver r_;  
    boost_asio::ip::tcp::socket sock_;  
};  
  
int main()  
{  
    while (true)  
    {  
        Client client;  
        client.Connect();  
        Sleep(1 * 1000);  
    }  
}  
```  
  
This is due to a call to getaddrinfo in the resolver object (https://github.com/boostorg/asio/blob/develop/include/boost/asio/detail/impl/socket_ops.ipp#L3381). The call duplicates the handle of the calling thread these handles are then released when the thread completes. With boost::asio::detail::win_thread::set_terminate_threads set the thread is suddenly terminated and these duplicated handles are not released. Is this expected behavior?

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:13:11 UTC  
> Url: https://github.com/boostorg/asio/issues/326#issuecomment-752293464  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#748](https://github.com/chriskohlhoff/asio/issues/748).
