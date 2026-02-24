# #406 - Leak in boost::asio::detail::posix_event::posix_event() [Open]

> Username: pdekeister  
> Created at: 2023-02-22 15:36:05 UTC  
> Updated at: 2023-02-22 15:52:41 UTC  
> Url: https://github.com/boostorg/asio/issues/406  

Valgrind analysis gived  
  
```  
 8 bytes in 1 blocks are definitely lost in loss record 6 of 57  
    at 0x4C287C4: malloc  
    by 0xEA260D2: pthread_condattr_init   
    by 0x74459DE: boost::asio::detail::posix_event::posix_event()   
    by 0x74452BC: boost::asio::detail::scheduler::scheduler(boost::asio::execution_context&, int, bool)   
    by 0x9D3CE22: boost::asio::io_context::io_context()   
```  
  
  
pthread_condattr_destroy is missing   
I've made a patch seems to correct   
  
```  
--- boost/asio/detail/impl/posix_event.ipp.orig  
+++ boost/asio/detail/impl/posix_event.ipp  
@@ -45,6 +45,7 @@ posix_event::posix_event()  
 #endif // (defined(__MACH__) && defined(__APPLE__))  
        // || (defined(__ANDROID__) && (__ANDROID_API__ < 21))  
   
+  ::pthread_condattr_destroy(&attr);  
   boost::system::error_code ec(error,  
       boost::asio::error::get_system_category());  
   boost::asio::detail::throw_error(ec, "event");  
```
