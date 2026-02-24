# #319 - CPU Affinity support [Open]

> Username: kuhlenough  
> Created at: 2020-05-28 17:40:36 UTC  
> Updated at: 2020-05-28 17:40:36 UTC  
> Url: https://github.com/boostorg/thread/issues/319  

Recent _"Best practice"_ in embedded Linux and VxWorks to achieve **deterministic** results for critical functionality is to use "core reservation" at boot and take one or more CPUs out of the general pool of CPUs used for SMP scheduling.      
Threads requiring determinism then use the platform dependent API  schedule themselves on the reserved core.  This is a request for a thread attribute that  abstracts the platform specific API for affinity as part of boost::thread  (not  hidden in the coroutine2  benchmarking )  https://github.com/boostorg/coroutine2/tree/develop/performance
