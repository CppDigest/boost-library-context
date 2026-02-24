# #344 - Uncaught exception in ‘boost/thread/lock_types.hpp’ [Open]

> Username: dimitrov-ds  
> Created at: 2021-06-08 08:31:42 UTC  
> Updated at: 2021-06-08 08:31:42 UTC  
> Url: https://github.com/boostorg/thread/issues/344  

Hi,  
I've just run Coverity, version 2020.03, static analysis on a program using Boost v 1_75_0  
It has highlighted several potential issues one of which I will describe here.  
  
In lock_types.hpp on line 632 the destructor of the ‘shared_lock’ class calls the unlock_shared() method of the mutex. This method can throw an exception of type boost::lock_error. However, although the shared_lock destructor is not explicitly declared as ‘noexcept’ the compiler will use the exception specification from the hypothetical implicitly-generated destructor definition. This way it will deduce noexcept(true) for this destructor. Then if an exception is thrown from the destructor’s body this will result in calling unexpected() which calls terminate(). Synopsis Coverity static analysis tool highlighted this problem. In my opinion the described problem can happen. Do you think so or I am missing something?  
  
Thanks,  
Dimitar
