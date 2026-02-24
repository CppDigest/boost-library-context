# #416 - asio::ip::tcp::accept.close cause accept::async_accept run error [Open]

> Username: ll596214569  
> Created at: 2023-04-12 10:08:32 UTC  
> Updated at: 2025-03-12 11:19:28 UTC  
> Url: https://github.com/boostorg/asio/issues/416  

hi, I have a problem, after calling "accept.close", "accept.async_accept" is triggered, but "accept.is_open" is still true, causing my program to do something wrong  
  
```  
#pragma once  
#ifndef __test_acceptor__  
#define __test_acceptor__  
  
#include "boost/noncopyable.hpp"  
class test_acceptor : private boost::noncopyable, public std::enable_shared_from_this<test_acceptor>  
{  
public:  
    using acceptor_type = boost::asio::ip::tcp::acceptor;  
    using resolver_type = boost::asio::ip::tcp::resolver;  
    using io_type = boost::asio::io_context;  
    using resolver_query_type = boost::asio::ip::tcp::resolver::query;  
    using endpoint_type = boost::asio::ip::tcp::endpoint;  
    using string_type = std::string;  
    wh_async_acceptor(io_type& _io, const string_type& _straddr, const string_type& _strport);  
    ~wh_async_acceptor();  
public:  
    void start();  
  
    void stop();  
  
    void handle_accept();  
  
private:  
    io_type& io_;  
    acceptor_type acceptor_;  
}	  
#endif   
  
  
  
#include "test_accept.h"  
test_acceptor::wh_async_acceptor(io_type& _io, const string_type& _straddr, const string_type& _strport)   
    : io_(_io), acceptor_(io_)  
{  
    resolver_type _resolver(io_);  
    resolver_query_type _resolver_query(_straddr, _strport);  
    endpoint_type endpoint_ = *_resolver.resolve(_resolver_query).begin();  
    acceptor_.open(endpoint_.protocol());  
    acceptor_.set_option(boost::asio::ip::tcp::acceptor::reuse_address(true));  
    acceptor_.bind(endpoint_);  
    acceptor_.listen();  
}  
  
test_acceptor::~wh_async_acceptor()  
{  
}  
  
void test_acceptor::start()  
{  
    handle_accept();  
}  
  
void test_acceptor::stop()  
{  
    if (acceptor_.is_open())  
    {  
        acceptor_.close();  
    }  
}  
  
void wh_async_acceptor::handle_accept()  
{  
    acceptor_.async_accept([ptr = shared_from_this()](std::error_code _err, socket_type _socket) {  
        if (!ptr->acceptor_.is_open())  
        {  
            return;  
        }  
        if (!_err)  
        {  
            // todo   
        }  
        ptr->handle_accept();  
    });  
}  
```  
  
Here's what the code . When I call "test_acceptor::stop()", "wh_async_acceptor::handle_accept()" will still enter the " //todo" section, causing my program to fail . Can someone tell me why?

---

## Comment 1

> Username: Jackarain  
> Created at: 2025-03-12 11:19:27 UTC  
> Url: https://github.com/boostorg/asio/issues/416#issuecomment-2717531188  

The issue you're encountering is due to the asynchronous nature of Boost.Asio's async_accept operation. When you call acceptor_.close(), the acceptor's state transitions to closed, but any pending asynchronous operations (like those already queued in the event loop) will still invoke their completion handlers. This is why your //todo section is being executed even after stop() is called.  
The async_accept callback receives an error_code parameter which should be checked ​before accessing the acceptor's state. If the acceptor was closed, the error code will be boost::asio::error::operation_aborted, and you should exit the callback early.
