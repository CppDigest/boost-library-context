# #124 - exception thrown at exit on multithreaded app, something is missing? [Closed]

> Username: leobelen  
> Created at: 2020-08-12 04:12:46 UTC  
> Updated at: 2021-01-27 22:02:54 UTC  
> Closed at: 2021-01-27 22:02:54 UTC  
> Url: https://github.com/boostorg/log/issues/124  

On a multithreaded app on macOS Catalina, with homebrewed boost 1.73,  and threads provided by std::thread an exception is thrown on exit that states   
```   
uncaught exception of type boost::wrapexcept<boost::exception_detail::error_info_injector<boost::log::v2_mt_posix::system_error> >: Failed to set TLS value: Invalid argument  
```  
However, it is on the general winding down of the system, and the first line of the main function is path::imbue(std::locale("C")); and the last one is core::get()->remove_all_sinks();... is something missing?

---

## Comment 1

> Username: Lastique  
> Created at: 2020-08-12 08:36:52 UTC  
> Url: https://github.com/boostorg/log/issues/124#issuecomment-672735326  

I can't tell what's going on without seeing the reproducer code. The exception is due to `pthread_setspecific` failure, which is likely because it is called on an invalid TLS key. This can happen if you're doing something with Boost.Log in global destructors.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-09-08 13:10:21 UTC  
> Url: https://github.com/boostorg/log/issues/124#issuecomment-688855272  

Any updates on this?

---

## Comment 3

> Username: Lastique  
> Created at: 2021-01-27 22:02:54 UTC  
> Url: https://github.com/boostorg/log/issues/124#issuecomment-768608410  

No response.
