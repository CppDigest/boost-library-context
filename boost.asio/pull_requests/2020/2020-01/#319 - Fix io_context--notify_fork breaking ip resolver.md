# #319 Fix io_context::notify_fork breaking ip resolver [Closed]

> Username: RewritableJ  
> Created at: 2020-01-20 15:18:15 UTC  
> Updated at: 2020-12-30 01:29:08 UTC  
> Closed at: 2020-12-30 01:29:07 UTC  
> Url: https://github.com/boostorg/asio/pull/319  

Consider following example:  
```  
#include <iostream>  
#include <stdexcept>  
#include <memory>  
  
#include <boost/asio.hpp>  
  
#include <unistd.h>  
  
namespace ba = boost::asio;  
  
std::unique_ptr< ba::io_context > ioc;  
std::unique_ptr< ba::ip::tcp::resolver > resolver;  
bool is_first = true;  
  
void do_cycle();  
  
void handle_resolve(const boost::system::error_code &ec, const ba::ip::tcp::resolver::results_type & results)  
{  
    if (ec)  
        throw std::runtime_error("Resolve: "  + ec.message());  
  
    if (is_first)  
    {  
        std::cout << "Resolved once" << std::endl;  
  
        is_first = false;  
  
        ioc->notify_fork(ba::io_context::fork_prepare);  
  
        std::cout << "Forking" << std::endl;  
        if (fork() == 0)  
        {  
            setsid();  
            ioc->notify_fork(ba::io_context::fork_child);  
            do_cycle();  
        }  
        else  
        {  
            ioc->notify_fork(ba::io_context::fork_parent);  
            ioc->stop();  
        }  
    }  
    else  
    {  
        std::cout << "Resolved twice" << std::endl;  
    }  
};  
  
void do_cycle()  
{  
    std::cout << "Resolving " << getpid() << std::endl;  
    resolver->async_resolve(ba::ip::tcp::resolver::query("www.boost.org", "http"), &handle_resolve);  
};  
  
  
int main()  
{  
    ioc = std::make_unique< ba::io_context >();  
    resolver = std::make_unique< ba::ip::tcp::resolver >(*ioc);  
  
    do_cycle();  
    ioc->run();  
  
    return 0;  
}  
```  
Second `async_resolve` never starts. First `io_context::notify_fork` stops `resolver_service_base`'s internal scheduler. Following calls to `io_context::notify_fork` don't reset it what breaks every resolver associated with a context.

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2020-12-30 01:29:07 UTC  
> Url: https://github.com/boostorg/asio/pull/319#issuecomment-752295997  

Fixed in asio 1.16.1 / boost 1.73.

---
