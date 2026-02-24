# #86 - Stack smashing in boost::asio::detail::task_io_service::do_run_one [Closed]

> Username: ghost  
> Created at: 2018-02-17 05:19:02 UTC  
> Updated at: 2020-12-30 00:49:05 UTC  
> Closed at: 2020-12-30 00:48:56 UTC  
> Url: https://github.com/boostorg/asio/issues/86  

I have a stripped down PoC and some more info here:  
  
https://pastebin.com/raw/GVK8PJRe  
  
Looking at the function in question, I don't see anything that immediately stands out, but I'm not familiar with all the internal processes going on within it.  
  
**Build/Test (Boost 1.62.0,  g++ 7.2.0, Linux 4.13.0-32-generic x86_64):**  
  
Start echo server  
```ncat -vvlp 9001 -e /bin/cat```  
  
Run  
``` g++ main.cpp -lboost_system -lpthread```  
``` ./a.out 127.0.0.1 9001```  
  
> *** stack smashing detected ***: <unknow> terminated  
> Aborted (core dumped)  
>  
> Backtrace:  
> 1  __GI_raise                            
> 2  __GI_abort                            
> 3  __libc_message                        
> 4  __GI___fortify_fail_abort             
> 5  __stack_chk_fail                      
> 6  boost::asio::detail::task_io_service::do_run_one(boost::asio::detail::scoped_lock<boost::asio::detail::posix_mutex>&, boost::asio::detail::task_io_service_thread_info&, boost::system::error_code const&)   
> 7  boost::asio::detail::task_io_service::run_one(boost::system::error_code&)         
> 8  boost::asio::io_service::run_one()         
> 9  tcp::send(std::vector<unsigned char> const&)          
> 10 main

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:48:44 UTC  
> Url: https://github.com/boostorg/asio/issues/86#issuecomment-752289016  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#599](https://github.com/chriskohlhoff/asio/issues/599).
