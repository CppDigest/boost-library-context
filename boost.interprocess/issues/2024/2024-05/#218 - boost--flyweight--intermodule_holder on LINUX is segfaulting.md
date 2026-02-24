# #218 - boost::flyweight::intermodule_holder on LINUX is segfaulting [Open]

> Username: igaztanaga  
> Created at: 2024-05-09 21:07:12 UTC  
> Updated at: 2024-05-09 21:07:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/218  

Reported in the Boost mailing list:  
  
https://lists.boost.org/boost-users/2024/05/91583.php  
  
Apparently the boost::flyweight::intermodule_holder on LINUX is creating shared memory objects, which by accident (crash?) can persist after the process having created them is already gone.  
  
As a result the code can attempt to create an already existing shared memory object created by another user.  
  
It seems like locking is based on a unique temporary filesystem path that uses both the process ID and its start time:  
  
So, the code should be robust against dangling locks because the combination (PID, start time) won't ever repeat. Maybe you can debug your program and try to isolate the actual path generated for the offending scenario?  
  
It might be related with the lame implementation of get_current_process_creation_time in Linux.
