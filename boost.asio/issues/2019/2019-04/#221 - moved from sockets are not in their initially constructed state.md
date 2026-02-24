# #221 - moved from sockets are not in their initially constructed state [Closed]

> Username: seelabs  
> Created at: 2019-04-10 14:35:41 UTC  
> Updated at: 2019-04-10 15:05:52 UTC  
> Closed at: 2019-04-10 15:05:52 UTC  
> Url: https://github.com/boostorg/asio/issues/221  

According to this doc: https://www.boost.org/doc/libs/1_69_0/doc/html/boost_asio/reference/basic_stream_socket/basic_stream_socket/overload5.html A moved-from socket should be in the same sate as if constructed using the `basic_stream_socket(io_context&) constructor`. However, in boost 1.70 beta 1, the executor in a moved from socket appears to be different.  
  
The following function will reproduce the issue:  
```  
  
void  
moveSocketTest()  
{  
    namespace asio = boost::asio;  
    using work = asio::executor_work_guard<asio::executor>;  
    asio::io_context ioc;  
  
    asio::ip::tcp::socket s1{ioc};  
    {  
        work ww{s1.get_executor()};  
    }  
    {  
        asio::ip::tcp::socket s2{std::move(s1)};  
        work ww{s1.get_executor()};  
    }  
}  
```  
In boost 1.69, that runs without issue. In boost 1.70 beta 1, that throws a `bad executor` exception.

---

## Comment 1

> Username: seelabs  
> Created at: 2019-04-10 15:05:52 UTC  
> Url: https://github.com/boostorg/asio/issues/221#issuecomment-481729981  

This appears to be fixed in 1.70 rc2 (likely from this patch: https://github.com/boostorg/asio/commit/e830f97d55c84a0078e307d8c8817e138f4483f2) Closing this issue.
