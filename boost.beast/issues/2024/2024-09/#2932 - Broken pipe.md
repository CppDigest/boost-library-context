# #2932 - Broken pipe [system:32] [Closed]

> Username: xiaoma565  
> Created at: 2024-09-20 17:15:09 UTC  
> Updated at: 2024-09-23 16:45:37 UTC  
> Closed at: 2024-09-23 16:45:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2932  

I got a error  
```c++  
terminate called after throwing an instance of 'boost::wrapexcept<boost::system::system_error>'  
  what():  Broken pipe [system:32]  
```   
And my stack is here  
```c++  
(gdb) bt  
#0  __pthread_kill_implementation (threadid=281474814308608, signo=signo@entry=6, no_tid=no_tid@entry=0) at ./nptl/pthread_kill.c:44  
#1  0x0000fffff6ebf254 in __pthread_kill_internal (signo=6, threadid=<optimized out>) at ./nptl/pthread_kill.c:78  
#2  0x0000fffff6e7a67c in __GI_raise (sig=sig@entry=6) at ../sysdeps/posix/raise.c:26  
#3  0x0000fffff6e67130 in __GI_abort () at ./stdlib/abort.c:79  
#4  0x0000fffff716527c in __gnu_cxx::__verbose_terminate_handler() () from /lib/aarch64-linux-gnu/libstdc++.so.6  
#5  0x0000fffff7162a5c in ?? () from /lib/aarch64-linux-gnu/libstdc++.so.6  
#6  0x0000fffff7162ac0 in std::terminate() () from /lib/aarch64-linux-gnu/libstdc++.so.6  
#7  0x0000fffff7162a48 in std::rethrow_exception(std::__exception_ptr::exception_ptr) () from /lib/aarch64-linux-gnu/libstdc++.so.6  
#8  0x000000000057295c in boost::asio::detail::thread_info_base::rethrow_pending_exception (this=0xfffff651e5d0)  
    at /boost/boost/asio/detail/thread_info_base.hpp:234  
#9  0x000000000057f8b8 in boost::asio::detail::scheduler::do_run_one (this=0x1bbeeb0, lock=..., this_thread=..., ec=...)  
    at /boost/boost/asio/detail/impl/scheduler.ipp:494  
#10 0x000000000057e540 in boost::asio::detail::scheduler::run (this=0x1bbeeb0, ec=...) at /boost/boost/asio/detail/impl/scheduler.ipp:210  
#11 0x0000000000580538 in boost::asio::io_context::run (this=0x1bbee90) at /boost/boost/asio/impl/io_context.ipp:64  
#12 0x0000000000fc6068 in <lambda()>::operator()(void) const (__closure=0x1bbf018) at /io_context_pool/HttpIoCtxPool.cpp:49  
#13 0x0000000000fc7d94 in std::__invoke_impl<void, MINDIE::MS::HttpIoCtxPool::Run()::<lambda()> >(std::__invoke_other, <lambda()> &&) (__f=...)  
    at /opt/buildtools/gcc_7.3.0/include/c++/7.3.0/bits/invoke.h:60  
#14 0x0000000000fc76f4 in std::__invoke<MINDIE::MS::HttpIoCtxPool::Run()::<lambda()> >(<lambda()> &&) (__fn=...) at /opt/buildtools/gcc_7.3.0/include/c++/7.3.0/bits/invoke.h:95  
#15 0x0000000000fc8cc4 in std::thread::_Invoker<std::tuple<MINDIE::MS::HttpIoCtxPool::Run()::<lambda()> > >::_M_invoke<0>(std::_Index_tuple<0>) (this=0x1bbf018)  
    at /opt/buildtools/gcc_7.3.0/include/c++/7.3.0/thread:234  
#16 0x0000000000fc8bdc in std::thread::_Invoker<std::tuple<MINDIE::MS::HttpIoCtxPool::Run()::<lambda()> > >::operator()(void) (this=0x1bbf018) at /opt/buildtools/gcc_7.3.0/include/c++/7.3.0/thread:243  
#17 0x0000000000fc8b44 in std::thread::_State_impl<std::thread::_Invoker<std::tuple<MINDIE::MS::HttpIoCtxPool::Run()::<lambda()> > > >::_M_run(void) (this=0x1bbf010)  
    at /opt/buildtools/gcc_7.3.0/include/c++/7.3.0/thread:186  
#18 0x0000fffff719224c in ?? () from /lib/aarch64-linux-gnu/libstdc++.so.6  
#19 0x0000fffff6ebd5c8 in start_thread (arg=0x0) at ./nptl/pthread_create.c:442  
#20 0x0000fffff6f25edc in thread_start () at ../sysdeps/unix/sysv/linux/aarch64/clone.S:79  
```   
  
I find the answer from web  
![image](https://github.com/user-attachments/assets/bf8a5025-c423-4869-919e-f5303412605b)  
  
How to prevent this execption? How to catch it?

---

## Comment 1

> Username: sehe  
> Created at: 2024-09-20 17:48:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2932#issuecomment-2364220201  

Exceptions can be handled by catching them. In this case, you might catch it by surrounding the `run()` call with an appropriate `try/catch`  
  
     try {  
            ioc.run();  
     } catch(boost::system::system_error const& se) {  
           std::cerr << "Handled the error " << se.code().message() << std::endl;  
     }  
  
See also https://stackoverflow.com/questions/44500818/should-the-exception-thrown-by-boostasioio-servicerun-be-caught  
  
It is usually more productive to catch closer to where the error is being raised. In your debugger, find when the exception is thrown (or analyze the IO operations to see which one likely throws this)

---

## Comment 2

> Username: vinniefalco  
> Created at: 2024-09-20 18:27:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2932#issuecomment-2364295486  

Consider using the APIs which indicate errors using `error_code`

---

## Comment 3

> Username: xiaoma565  
> Created at: 2024-09-20 18:31:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2932#issuecomment-2364301527  

> Exceptions can be handled by catching them. In this case, you might catch it by surrounding the `run()` call with an appropriate `try/catch`  
>   
> ```  
>  try {  
>         ioc.run();  
>  } catch(boost::system::system_error const& se) {  
>        std::cerr << "Handled the error " << se.code().message() << std::endl;  
>  }  
> ```  
>   
> See also https://stackoverflow.com/questions/44500818/should-the-exception-thrown-by-boostasioio-servicerun-be-caught  
>   
> It is usually more productive to catch closer to where the error is being raised. In your debugger, find when the exception is thrown (or analyze the IO operations to see which one likely throws this)  
  
Is there any other way to solve this problem? I don't want io_context to stop running.

---

## Comment 4

> Username: sehe  
> Created at: 2024-09-20 18:35:40 UTC  
> Updated at: 2024-09-20 18:36:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2932#issuecomment-2364310791  

Yes:  
  
> Consider using the APIs which indicate errors using error_code  
  
If you show actual code we can help you with the specific code. But really it all comes down to just handling exceptions (or avoiding them)

---

## Comment 5

> Username: xiaoma565  
> Created at: 2024-09-20 18:45:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2932#issuecomment-2364327338  

> Consider using the APIs which indicate errors using `error_code`  
  
I use tcp stream with async_write and async_read. I had bind handler function but the exception is still been thrown. My code is a little complicated. I made it with boost example async_server and chat_server.  
  
> Yes:  
>   
> > Consider using the APIs which indicate errors using error_code  
>   
> If you show actual code we can help you with the specific code. But really it all comes down to just handling exceptions (or avoiding them)  
  
I use tcp stream with async_write and async_read. I had bind handler function but the exception is still been thrown. My code is a little complicated. I made it with boost example async_server and chat_server.

---

## Comment 6

> Username: sehe  
> Created at: 2024-09-20 18:59:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2932#issuecomment-2364347127  

Assuming that you mean to imply you somehow cannot show the code, I suggest again to figure out where the exception is thrown from using a debugger.  
  
If the code is based on the two small Asio examples, I'd think it shouldn't be too big to show. You could also put it into a gist or something.

---

## Comment 7

> Username: xiaoma565  
> Created at: 2024-09-20 18:59:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2932#issuecomment-2364347465  

issues/2908  My code is here

---

## Comment 8

> Username: xiaoma565  
> Created at: 2024-09-20 19:08:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2932#issuecomment-2364360018  

> Assuming that you mean to imply you somehow cannot show the code, I suggest again to figure out where the exception is thrown from using a debugger.  
>   
> If the code is based on the two small Asio examples, I'd think it shouldn't be too big to show. You could also put it into a gist or something.  
  
hah, I have gone home. I will upload my  
new code tomorrow.

---

## Comment 9

> Username: sehe  
> Created at: 2024-09-20 22:20:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2932#issuecomment-2364693986  

I'm a bit confused by the complexities. Allow me to introduce [the buffers_generator concept](https://www.boost.org/doc/libs/1_86_0/libs/beast/doc/html/beast/concepts/BuffersGenerator.html) which can be used to abstract different types of responses.  
  
To generate buffers for the standard Beast `http::response` message types use `http::message_generator`.  
  
Next, add error handling around the `shutdown` call. This self-contained simplified version of your code works well for me: https://gist.github.com/sehe/9c48b7089eafaae05ffef16a5587af22  
  
```c++  
#include "HttpServer.h"  
  
int main() {  
    HttpServer    server(10, 60, 4);  
    ServerHandler sh;  
  
    sh.RegisterFun(http::verb::get, "/", [&server](Connection conn) {  
        conn->SendRes(http::status::ok, "text/html",  
                      "<html><body><h1>Hello, World!</h1>Connections: " +  
                          std::to_string(server.GetConnectionsCount()) + "</body></html>\r\n");  
    });  
  
    RequestHandler echo = [](Connection conn) {  
        auto& req = conn->GetReq();  
        LOG("Handling '/echo' %s\n", http::to_string(req.method()).data());  
        std::ostringstream oss;  
        oss << req;  
        http::response<http::string_body> res{http::status::ok, req.version(), oss.str()};  
  
        res.set(http::field::content_type, "text/plain");  
        res.keep_alive(req.keep_alive());  
        res.prepare_payload();  
        conn->SendRes(std::move(res));  
    };  
    sh.RegisterFun(http::verb::put,     "/echo", echo);  
    sh.RegisterFun(http::verb::get,     "/echo", echo);  
    sh.RegisterFun(http::verb::post,    "/echo", echo);  
    sh.RegisterFun(http::verb::delete_, "/echo", echo);  
  
    sh.RegisterFun(ServerHandler::HandlerType::REQ_ERROR, [](Connection conn) {  
        LOG("Handling error\n");  
        conn->SendRes(http::response<http::string_body>{http::status::internal_server_error, 10,  
                                                        "Internal Server Error"});  
    });  
    server.Run("0.0.0.0", "8989", sh);  
}  
```  
  
With local demo:  
  
[Screencast from 2024-09-21 00-10-29.webm](https://github.com/user-attachments/assets/6d44de08-5967-47cb-aed3-1839eca5d24f)
