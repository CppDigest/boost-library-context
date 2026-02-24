# #409 - SIGSEGV at _dl_runtime_resolve_xsavec in boost::detail::set_current_thread_data [Open]

> Username: eroller  
> Created at: 2024-11-12 18:11:40 UTC  
> Updated at: 2024-11-12 18:12:02 UTC  
> Url: https://github.com/boostorg/thread/issues/409  

I started running into this SIGSEGV. Is it possible that some thread-local variable initialization could trigger this? Any ideas for how to troubleshoot further? When I swap out boost::thread with std::thread, the SIGSEGV goes away. So far I haven't been able to reproduce the error with a simple example. I am using boost 1.84.0  
  
 ```  
  Thread 5 "a.out" received signal SIGSEGV, Segmentation fault.  
  [Switching to Thread 0x7fffe8704700 (LWP 700154)]  
  _dl_runtime_resolve_xsavec () at ../sysdeps/x86_64/dl-trampoline.h:126  
  126     ../sysdeps/x86_64/dl-trampoline.h: No such file or directory.  
  (gdb) bt  
  #0  _dl_runtime_resolve_xsavec () at ../sysdeps/x86_64/dl-trampoline.h:126  
  #1  0x0000000006869d48 in boost::detail::set_current_thread_data(boost::detail::thread_data_base*) ()  
  #2  0x000000000686af07 in thread_proxy ()  
  #3  0x00007ffff77761da in start_thread () from /lib64/libpthread.so.0  
  #4  0x00007ffff361a8d3 in clone () from /lib64/libc.so.6  
```
