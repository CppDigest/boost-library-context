# #180 - Async IO / operation with "Event" without callback mechanism [Closed]

> Username: KaoCC  
> Created at: 2018-08-23 17:11:11 UTC  
> Updated at: 2018-08-27 12:12:21 UTC  
> Closed at: 2018-08-27 07:35:04 UTC  
> Url: https://github.com/boostorg/fiber/issues/180  

Hello.  
  
I'd like to ask how to integrate fiber with other frameworks that include asynchronous I/O API but only return an "event" to be wait for (the wait will block the thread). These frameworks may not have the APIs for registering a proper callback function.  
  
Imagine a simple example as an OpenCL wrapper that wraps clEnqueueNDRangeKernel but did not expose the clSetEventCallback functionality.  
  
In addition, for Boost.Compute can we register a fiber promise which is to be invoked in a callback in the ".then(Callback)" function which is from the future returned by "copy_async" ?

---

## Comment 1

> Username: olk  
> Created at: 2018-08-27 07:35:04 UTC  
> Url: https://github.com/boostorg/fiber/issues/180#issuecomment-416141400  

bosot.fiber will not work with frameworks that block the thread ... if a thread is blocked no other ready fiber can be executed.  
  
In context of accelerator APIS - take a look at the CUDA/HIP examples. The thread is not blocked while some fiber wait on events from CUDA/HIP streams.

---

## Comment 2

> Username: KaoCC  
> Created at: 2018-08-27 12:12:21 UTC  
> Url: https://github.com/boostorg/fiber/issues/180#issuecomment-416206445  

Thank for the reply !
