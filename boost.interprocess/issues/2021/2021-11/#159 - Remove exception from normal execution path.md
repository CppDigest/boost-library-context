# #159 - Remove exception from normal execution path [Open]

> Username: daravi  
> Created at: 2021-11-09 23:48:19 UTC  
> Updated at: 2021-11-09 23:50:16 UTC  
> Url: https://github.com/boostorg/interprocess/issues/159  

As it is, using `boost::interprocess::open_or_create` in the constructors can lead to exceptions that are internally caught. This makes debugging hard as I have to skip those exceptions until I get to an actual "exception". IMO an "exception" should only be thrown in exceptional circumstances and not during normal operation.  
  
If possible please add a way check if a shared memory segment exists given the key without needing to throw an exception. I think this would also help with the usability of the library.
