# #267 - Segmentation fault in boost tss, thread_specific_ptr [Open]

> Username: Red-Portal  
> Created at: 2019-01-15 18:47:23 UTC  
> Updated at: 2019-05-09 19:29:31 UTC  
> Url: https://github.com/boostorg/thread/issues/267  

Encountered a segmentation fault while using boost::thread_specific_ptr.  
It worked completely fine on my laptop but it doesn't work on our NUMA cluster.  
  
```  
Thread 1 "cg.C" received signal SIGSEGV, Segmentation fault.  
boost::thread_specific_ptr<std::chrono::time_point<std::chrono::_V2::steady_clock, std::chrono::duration<long, std::ratio<1l, 1000000000l> > > >::thread_specific_ptr (this=0x7ffff7ba2b00 <_start>) at /usr/local/include/boost/thread/tss.hpp:68  
68	            cleanup(detail::heap_new<delete_data>(),detail::do_heap_delete<delete_data>())  
```  
  
This is caused during global variable initialization.  
I tried to wrap the ```boost::thread_specific_ptr```s around using ```std::optional``` to delay the initialization, and it worked.  
Seems like there is some problem during dynamic library loading.  
It was the same when,  
1. statically linked boost into a dynamic library and linking  it into an executable  
2. dynamically loading boost into the exectuable   
Unfortunately, I can't test statically linking boost to the executable right now.  
  
In case of the ```std::optional``` fix, a segmentation fault is caused during dynamic library unloading.  
(see below)  
  
```  
Thread 1 "cg.C" received signal SIGSEGV, Segmentation fault.  
boost::detail::shared_count::~shared_count (this=<optimized out>, __in_chrg=<optimized out>)  
    at /usr/local/include/boost/smart_ptr/detail/shared_count.hpp:426  
426	        if( pi_ != 0 ) pi_->release();  
(gdb) quit  
A debugging session is active.  
  
	Inferior 1 [process 79251] will be killed.  
  
Quit anyway? (y or n) y  
```

---

## Comment 1

> Username: viboes  
> Created at: 2019-01-15 20:44:17 UTC  
> Url: https://github.com/boostorg/thread/issues/267#issuecomment-454544182  

It is know that boost::thread_specific_ptr is bugged in a dynamic library context.  
  
If someone has time to spend ....

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-01-28 18:26:34 UTC  
> Url: https://github.com/boostorg/thread/issues/267#issuecomment-458245848  

Try the latest master branch, it has #249 that might solved your issue.
