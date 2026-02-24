# #376 - Undefined symbol "boost::this_thread::interruption_point()" [Open]

> Username: clankill3r  
> Created at: 2022-08-21 08:37:29 UTC  
> Updated at: 2022-08-21 15:14:25 UTC  
> Url: https://github.com/boostorg/thread/issues/376  

I'm using this library:  
https://docs.websocketpp.org/faq.html  
  
All the examples will build except one (https://github.com/zaphoyd/websocketpp/tree/master/examples/sip_client).  
The one that does not build has one line it fails on which is the following line:  
  
`boost::this_thread::sleep(boost::posix_time::milliseconds(100));`  
  
Which gives the following error:  
  
```  
Undefined symbols for architecture x86_64:  
  "boost::this_thread::interruption_point()", referenced from:  
      boost::condition_variable::wait(boost::unique_lock<boost::mutex>&) in sip_client.cpp.o  
      boost::condition_variable::do_wait_until(boost::unique_lock<boost::mutex>&, boost::detail::real_platform_timepoint const&) in sip_client.cpp.o  
  "boost::detail::get_current_thread_data()", referenced from:  
      boost::detail::interruption_checker::interruption_checker(_opaque_pthread_mutex_t*, _opaque_pthread_cond_t*) in sip_client.cpp.o  
ld: symbol(s) not found for architecture x86_64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
make[2]: *** [bin/sip_client] Error 1  
make[1]: *** [examples/sip_client/CMakeFiles/sip_client.dir/all] Error 2  
make: *** [all] Error 2  
```  
  
I tried it with boost 1_39_0, 1_46_1 and 1_80_0.  
  
I'm on OSX 10.15.7. Let me know if I can help in any way.
