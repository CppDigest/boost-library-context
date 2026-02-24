# #345 - Uncaught exception in ‘boost/thread/lock_guard.hpp’ [Open]

> Username: dimitrov-ds  
> Created at: 2021-06-08 08:39:24 UTC  
> Updated at: 2021-06-08 08:39:38 UTC  
> Url: https://github.com/boostorg/thread/issues/345  

Hi,  
I've just run Coverity, version 2020.03, static analysis on a program using Boost v 1_75_0  
It has highlighted several potential issues one of which I will describe here.  
  
In lock_guard.hpp on line 67 the destructor calls the unlock() method of the mutex ‘m’. The unlock() can throw an exception of type boost::lock_error. However, since the C++ 11 compiler deduces the exception specification for this constructor it will use ‘noexcept(true)’ which would be the exception specification of the hypothetical implicitly-generated destructor definition. This way, if and exception is thrown from the destructor’s body and the exception specification does not allow it to be thrown this will lead to calling unexpected() which calls terminate(). Synopsis Coverity static analysis tool highlighted this problem. In my opinion the described problem can happen. Do you think so or I am missing something?  
  
Thanks,  
Dimitar
