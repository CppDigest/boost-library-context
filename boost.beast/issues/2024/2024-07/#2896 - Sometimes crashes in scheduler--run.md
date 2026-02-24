# #2896 - Sometimes crashes in scheduler::run [Closed]

> Username: romanholidaypancakes  
> Created at: 2024-07-04 16:05:56 UTC  
> Updated at: 2024-07-04 16:30:31 UTC  
> Closed at: 2024-07-04 16:30:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2896  

### Version of Beast  
  
BOOST_BEAST_VERSION 347  
  
### Steps necessary to reproduce the problem  
The code is similar to  
```C++  
int main(int argc, char* argv[])  
{  
    // The io_context is required for all I/O  
    net::io_context ioc{threads};  
  
    // Create and launch a listening port  
    std::make_shared<listener>(  
        ioc,  
        tcp::endpoint{address, port},  
        doc_root)->run();  
  
    // Run the I/O service on the requested number of threads  
    std::vector<std::thread> v;  
    v.reserve(threads - 1);  
    for(auto i = threads - 1; i > 0; --i)  
        v.emplace_back(  
        [&ioc]  
        {  
            ioc.run();  
        });  
    ioc.run();  
  
    return EXIT_SUCCESS;  
}  
  
```  
Sometimes it crashes here  
  
```  
#0  __pthread_kill_implementation (threadid=<optimized out>, signo=signo@entry=6, no_tid=no_tid@entry=0) at ./nptl/pthread_kill.c:44  
#1  0x00007f1f5d3cad9f in __pthread_kill_internal (signo=6, threadid=<optimized out>) at ./nptl/pthread_kill.c:78  
#2  0x00007f1f5d37bf32 in __GI_raise (sig=sig@entry=6) at ../sysdeps/posix/raise.c:26  
#3  0x00007f1f5d366472 in __GI_abort () at ./stdlib/abort.c:79  
#4  0x00007f1f5d69d919 in ?? () from /lib/x86_64-linux-gnu/libstdc++.so.6  
#5  0x00007f1f5d6a8e1a in ?? () from /lib/x86_64-linux-gnu/libstdc++.so.6  
#6  0x00007f1f5d6a8e85 in std::terminate() () from /lib/x86_64-linux-gnu/libstdc++.so.6  
#7  0x00007f1f5d6a8e0e in std::rethrow_exception(std::__exception_ptr::exception_ptr) () from /lib/x86_64-linux-gnu/libstdc++.so.6  
#8  0x0000558b9fa36644 in boost::asio::detail::scheduler::run(boost::system::error_code&) [clone .isra.0] ()  
#9  0x0000558b9fa366cf in std::thread::_State_impl<std::thread::_Invoker<std::tuple<BeastServer::run()::{lambda()#1}> > >::_M_run() ()  
#10 0x00007f1f5d6d44a3 in ?? () from /lib/x86_64-linux-gnu/libstdc++.so.6  
#11 0x00007f1f5d3c9044 in start_thread (arg=<optimized out>) at ./nptl/pthread_create.c:442  
#12 0x00007f1f5d44961c in clone3 () at ../sysdeps/unix/sysv/linux/x86_64/clone3.S:81  
```  
### All relevant compiler information  
  
gcc-12

---

## Comment 1

> Username: ashtum  
> Created at: 2024-07-04 16:18:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2896#issuecomment-2209303910  

It seems that an exception is thrown by one of the completion handlers in your code and rethrown by `io_context::run()`: [Effect of exceptions thrown from handlers](https://www.boost.org/doc/libs/1_85_0/doc/html/boost_asio/reference/io_context.html#boost_asio.reference.io_context.effect_of_exceptions_thrown_from_handlers).  
  
It would be helpful to see the contents of the exception, you can catch it like this:  
  
```C++  
try  
{  
  io_context.run();  
}  
catch (const std::exception& e)  
{  
  std::cerr << "Exception: " << e.what() << "\n";  
}  
```
