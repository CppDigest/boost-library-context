# #303 - Using io_service and steady_timer from different .so libraries - is it supported? [Closed]

> Username: Kh-Oleg  
> Created at: 2019-11-22 08:07:26 UTC  
> Updated at: 2020-12-30 01:11:40 UTC  
> Closed at: 2020-12-30 01:11:39 UTC  
> Url: https://github.com/boostorg/asio/issues/303  

I use some 3rd party library, which consists of the library itself, let's say libmain.so and an optional plugin, libplugin.so, loaded at runtime. Both of them use asio.  
  
libmain.so creates, owns and runs io_service instance and passes a reference to the plugin to initialize the timer.  
  
Problem: when io_service and timer are created in different .so modules, the timer is not triggered. When they are created in the same module - everything works.  
  
I did some investigation and it seems that the problem in boost::asio::detail::service_registry.   
It uses address of a static filed 'id' to identify a type and to check for duplicates in the registry.  
Addresses from different .so are different, so io_service listens (via select) for a wrong instance of internal timer service.  
  
My question: usage of io_service and timers from different .so modules - is it a supported use case?  
Platform: QNX Neutrino, boost asio: v. 1.61

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:11:38 UTC  
> Url: https://github.com/boostorg/asio/issues/303#issuecomment-752293255  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#733](https://github.com/chriskohlhoff/asio/issues/733).
