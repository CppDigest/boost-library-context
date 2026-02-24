# #28 - Assertion fails if cancellation is requested before connection establish [Closed]

> Username: nejati-deriv  
> Created at: 2022-09-22 13:13:05 UTC  
> Updated at: 2022-10-04 15:12:27 UTC  
> Closed at: 2022-10-04 15:12:27 UTC  
> Url: https://github.com/boostorg/redis/issues/28  

It seems when we send cancellation signal during the connection phase [this assertion fails](https://github.com/mzimbres/aedis/blob/798f193f14b01b44caccfebebcdedbea9432b1d6/include/aedis/detail/connection_ops.hpp#L536).  
i'm using https://github.com/mzimbres/aedis/tree/798f193f14b01b44caccfebebcdedbea9432b1d6  
`  
: ~/third_party/aedis/include/aedis/detail/connection_ops.hpp:536: void aedis::detail::reader_op<aedis::connection<>>::operator()(Self &, boost::system::error_code, std::size_t) [Conn = aedis::connection<>, Self = boost::asio::detail::composed_op<aedis::detail::reader_op<aedis::connection<>>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::experimental::detail::parallel_group_op_handler<0, boost::asio::experimental::wait_for_one_error, boost::asio::detail::composed_op<aedis::detail::start_op<aedis::connection<>>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::detail::composed_op<aedis::detail::run_op<aedis::connection<>>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::detail::as_tuple_handler<boost::asio::detail::awaitable_handler<boost::asio::any_io_executor, std::tuple<boost::system::error_code>>>, void (boost::system::error_code)>, void (boost::system::error_code)>, (lambda at ~/third_party/aedis/include/aedis/detail/connection_ops.hpp:358:13), (lambda at ~/third_party/aedis/include/aedis/detail/connection_ops.hpp:359:13), (lambda at ~/third_party/aedis/include/aedis/detail/connection_ops.hpp:360:13), (lambda at ~/third_party/aedis/include/aedis/detail/connection_ops.hpp:361:13)>, void (boost::system::error_code)>]: Assertion !conn->read_buffer_.empty()' failed.  
`

---

## Comment 1

> Username: nejati-deriv  
> Created at: 2022-09-22 13:44:12 UTC  
> Url: https://github.com/boostorg/redis/issues/28#issuecomment-1255046863  

You can easily reproduce it with simple loop like:  
  
```C++  
for (;;)  
{  
    auto timer = asio::steady_timer{ executor, std::chrono::milliseconds{ 10 } };  
    co_await (connection.async_run(endpoint, asio::use_awaitable) || timer.async_wait(asio::use_awaitable));  
}  
```

---

## Comment 2

> Username: mzimbres  
> Created at: 2022-09-22 15:43:13 UTC  
> Url: https://github.com/boostorg/redis/issues/28#issuecomment-1255214596  

Thanks for reporting. Aedis was designed to provide built-in timeouts. The call to   
```  
co_await (connection.async_run(endpoint, asio::use_awaitable) || timer.async_wait(asio::use_awaitable));  
```  
is probably messing around with the internal operations in a way I did not test before. I am considering disabling this type of cancellation unless there is a reason for not doing so. I want to avoid every user implementing timeouts on their side over and over again. For all possible timeouts see https://github.com/mzimbres/aedis/blob/fd82204ba9e6e85b1011c96ebff79539c4227ddf/include/aedis/connection.hpp#L46

---

## Comment 3

> Username: ashtum  
> Created at: 2022-09-22 16:03:17 UTC  
> Url: https://github.com/boostorg/redis/issues/28#issuecomment-1255238611  

Timeout here is just for demonstration, I'm not using timers like that I just cancel the connection for other reasons.  
Point is that async_run is not can cancellable properly.

---

## Comment 4

> Username: mzimbres  
> Created at: 2022-09-22 16:32:24 UTC  
> Url: https://github.com/boostorg/redis/issues/28#issuecomment-1255274488  

How are you cancelling, with `connection::cancel(operation::run)`? Do you observe the same behaviour if you cancel like this  
```  
timer.async_wait([&](auto){conn.cancel(operation::run)})  
```  
  
I will investigate this issue on the weekend.

---

## Comment 5

> Username: ashtum  
> Created at: 2022-09-22 16:43:46 UTC  
> Updated at: 2022-09-22 16:58:37 UTC  
> Url: https://github.com/boostorg/redis/issues/28#issuecomment-1255286246  

Haven't tested that, but because I am using it in coroutines, it needs to be cancellable because even if I use current coroutines cancellation slot it will get overwritten by `co_await conn.async_run(...)`

---

## Comment 6

> Username: mzimbres  
> Created at: 2022-09-24 09:25:13 UTC  
> Url: https://github.com/boostorg/redis/issues/28#issuecomment-1256921527  

I can't reproduce the crash with this  
  
```  
#include <chrono>  
#include <iostream>  
  
#include <boost/asio.hpp>  
#include <boost/asio/experimental/awaitable_operators.hpp>  
#include <aedis.hpp>  
#include <aedis/src.hpp>  
  
namespace net = boost::asio;  
using namespace net::experimental::awaitable_operators;  
using aedis::endpoint;  
using connection = aedis::connection<>;  
  
net::awaitable<void> reconnect(std::shared_ptr<connection> db)  
{  
   net::steady_timer timer{co_await net::this_coro::executor};  
   for (;;) {  
      timer.expires_after(std::chrono::milliseconds{10});  
      endpoint ep{"127.0.0.1", "6379"};  
      co_await (  
         db->async_run(ep, net::use_awaitable) ||  
         timer.async_wait(net::use_awaitable)  
      );  
      std::cout << "Retrying" << std::endl;  
   }  
}  
  
int main()  
{  
   net::io_context ioc;  
   auto db = std::make_shared<connection>(ioc);  
   net::co_spawn(ioc, reconnect(db), net::detached);  
   ioc.run();  
}  
```

---

## Comment 7

> Username: mzimbres  
> Created at: 2022-09-24 09:29:50 UTC  
> Url: https://github.com/boostorg/redis/issues/28#issuecomment-1256922905  

The crash on line `: ~/third_party/aedis/include/aedis/detail/connection_ops.hpp:536` is not possible on a normal cancellation. I am afraid this is indicating a buffer overflow somewhere in your program. Can you please enable sanitizers? For example, I get a crash if I use  
```  
      co_await (  
         db->async_run({"127.0.0.1", "6379"}, net::use_awaitable) ||  
         timer.async_wait(net::use_awaitable)  
      );  
```  
instead of  
```  
      endpoint ep{"127.0.0.1", "6379"};  
      co_await (  
         db->async_run(ep, net::use_awaitable) ||  
         timer.async_wait(net::use_awaitable)  
      );  
```

---

## Comment 8

> Username: nejati-deriv  
> Created at: 2022-09-25 16:08:06 UTC  
> Updated at: 2022-09-25 16:09:19 UTC  
> Url: https://github.com/boostorg/redis/issues/28#issuecomment-1257225375  

> I can't reproduce the crash with this  
>   
> ```  
> #include <chrono>  
> #include <iostream>  
>   
> #include <boost/asio.hpp>  
> #include <boost/asio/experimental/awaitable_operators.hpp>  
> #include <aedis.hpp>  
> #include <aedis/src.hpp>  
>   
> namespace net = boost::asio;  
> using namespace net::experimental::awaitable_operators;  
> using aedis::endpoint;  
> using connection = aedis::connection<>;  
>   
> net::awaitable<void> reconnect(std::shared_ptr<connection> db)  
> {  
>    net::steady_timer timer{co_await net::this_coro::executor};  
>    for (;;) {  
>       timer.expires_after(std::chrono::milliseconds{10});  
>       endpoint ep{"127.0.0.1", "6379"};  
>       co_await (  
>          db->async_run(ep, net::use_awaitable) ||  
>          timer.async_wait(net::use_awaitable)  
>       );  
>       std::cout << "Retrying" << std::endl;  
>    }  
> }  
>   
> int main()  
> {  
>    net::io_context ioc;  
>    auto db = std::make_shared<connection>(ioc);  
>    net::co_spawn(ioc, reconnect(db), net::detached);  
>    ioc.run();  
> }  
> ```  
  
I can reproduce it with your code here, it takes a little time to happen (most of the times under 60 secs). some times it leads to that assertion fail some times dead lock.  
Try change the time form 10ms to 6ms it happens faster.  
And of course make sure you are in debug build.  
Changing timer to something low like 1ms leads to a new assertion fail:  
`test: ~/third_party/aedis/include/aedis/detail/net.hpp:101: void aedis::detail::resolve_op<boost::asio::ip::basic_resolver<boost::asio::ip::tcp>, boost::asio::basic_waitable_timer<std::chrono::steady_clock>>::operator()(Self &, std::array<std::size_t, 2>, boost::system::error_code, boost::asio::ip::tcp::resolver::results_type, boost::system::error_code) [Resolver = boost::asio::ip::basic_resolver<boost::asio::ip::tcp>, Timer = boost::asio::basic_waitable_timer<std::chrono::steady_clock>, Self = boost::asio::detail::composed_op<aedis::detail::resolve_op<boost::asio::ip::basic_resolver<boost::asio::ip::tcp>, boost::asio::basic_waitable_timer<std::chrono::steady_clock>>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor, boost::asio::any_io_executor)>, boost::asio::detail::composed_op<aedis::detail::resolve_with_timeout_op<aedis::connection<>>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::detail::composed_op<aedis::detail::run_op<aedis::connection<>>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::detail::awaitable_handler<boost::asio::any_io_executor, boost::system::error_code>, void (boost::system::error_code)>, void (boost::system::error_code)>, void (boost::system::error_code, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>)>]: Assertion (!ec2)&&("resolve_op: Incompatible state.")' failed.`

---

## Comment 9

> Username: mzimbres  
> Created at: 2022-09-25 16:43:51 UTC  
> Url: https://github.com/boostorg/redis/issues/28#issuecomment-1257231771  

The second assertion you found is something I thought was impossible to happen but does not represent a pre condition for my code to work. I will remove it since it can indeed happen. Thanks again for reporting.

---

## Comment 10

> Username: mzimbres  
> Created at: 2022-09-25 20:49:32 UTC  
> Url: https://github.com/boostorg/redis/issues/28#issuecomment-1257276851  

This commit https://github.com/mzimbres/aedis/commit/3a03a43c060022dfb5001cdddb326ed460d9b54e fixes the problems I could reproduce so far. There is still a problem though if use very short timeouts like 1ms, in this case it seems that the cancellation gets lost. I will have to investigate more.

---

## Comment 11

> Username: nejati-deriv  
> Created at: 2022-09-26 04:57:59 UTC  
> Url: https://github.com/boostorg/redis/issues/28#issuecomment-1257483010  

Are you using the same commit that I mentioned in first comment?  
here is another assertion fail:  
`  
test: ~/third_party/aedis/include/aedis/resp3/detail/exec.hpp:133: void aedis::resp3::detail::exec_with_timeout_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::asio::basic_waitable_timer<std::chrono::steady_clock>, aedis::adapter::detail::general_aggregate<std::vector<aedis::resp3::node<std::string>>>, boost::asio::dynamic_string_buffer<char, std::char_traits<char>, std::allocator<char>>>::operator()(Self &, std::array<std::size_t, 2>, boost::system::error_code, std::size_t, boost::system::error_code) [AsyncStream = boost::asio::basic_stream_socket<boost::asio::ip::tcp>, Timer = boost::asio::basic_waitable_timer<std::chrono::steady_clock>, Adapter = aedis::adapter::detail::general_aggregate<std::vector<aedis::resp3::node<std::string>>>, DynamicBuffer = boost::asio::dynamic_string_buffer<char, std::char_traits<char>, std::allocator<char>>, Self = boost::asio::detail::composed_op<aedis::resp3::detail::exec_with_timeout_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::asio::basic_waitable_timer<std::chrono::steady_clock>, aedis::adapter::detail::general_aggregate<std::vector<aedis::resp3::node<std::string>>>, boost::asio::dynamic_string_buffer<char, std::char_traits<char>, std::allocator<char>>>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor, boost::asio::any_io_executor)>, boost::asio::detail::composed_op<aedis::detail::run_op<aedis::connection<>>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::detail::awaitable_handler<boost::asio::any_io_executor, boost::system::error_code>, void (boost::system::error_code)>, void (boost::system::error_code, unsigned long)>]: Assertion (!ec2)&&("exec_with_timeout_op: Unexpected completion order.")' failed.  
`  
  
Maybe using the following code helps to reproduce it faster:  
```C++  
  
#include <boost/asio.hpp>  
#include <boost/asio/experimental/awaitable_operators.hpp>  
  
#include <aedis.hpp>  
#include <aedis/src.hpp>  
#include <chrono>  
#include <iostream>  
  
namespace net = boost::asio;  
using namespace net::experimental::awaitable_operators;  
using aedis::endpoint;  
using connection = aedis::connection<>;  
  
net::awaitable< void >  
reconnect()  
{  
    auto executor = co_await net::this_coro::executor;  
    auto db1      = connection{ executor };  
    auto db2      = connection{ executor };  
    auto db3      = connection{ executor };  
    auto db4      = connection{ executor };  
    auto timer    = net::steady_timer{ executor };  
    for (int i = 0;; i++)  
    {  
        timer.expires_after(std::chrono::milliseconds{ 10 });  
        endpoint ep{ "127.0.0.1", "6379" };  
        co_await (db1.async_run(ep, net::use_awaitable) || db2.async_run(ep, net::use_awaitable) ||  
                  db3.async_run(ep, net::use_awaitable) || db4.async_run(ep, net::use_awaitable) ||  
                  timer.async_wait(net::use_awaitable));  
        std::cout << i << std::endl;  
    }  
}  
  
int  
main()  
{  
    net::io_context ioc;  
    net::co_spawn(ioc, reconnect(), net::detached);  
    ioc.run();  
}  
```

---

## Comment 12

> Username: mzimbres  
> Created at: 2022-10-02 07:58:20 UTC  
> Url: https://github.com/boostorg/redis/issues/28#issuecomment-1264578732  

I have created a new issue to improve cancellation support, see #32 so I will continue there. AFAICS, this ticket can be closed as the crashes have been fixed.
