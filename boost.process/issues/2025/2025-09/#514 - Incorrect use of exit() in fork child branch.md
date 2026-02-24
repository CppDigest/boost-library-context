# #514 - Incorrect use of exit() in fork child branch [Closed]

> Username: cmorve-te  
> Created at: 2025-09-01 10:24:06 UTC  
> Updated at: 2025-09-07 09:45:17 UTC  
> Closed at: 2025-09-07 09:45:17 UTC  
> Url: https://github.com/boostorg/process/issues/514  

If execve fails, the posix default launcher will [call exit()](https://github.com/boostorg/process/blob/5597aa0055b81017f6ea24cb652f07a014b12771/include/boost/process/v2/posix/default_launcher.hpp#L389). This is incorrect, since it can interfere with the parent; it should be calling [_Exit()](https://pubs.opengroup.org/onlinepubs/9799919799/functions/_Exit.html).  
  
See https://stackoverflow.com/questions/5422831/what-is-the-difference-between-using-exit-exit-in-a-conventional-linux-fo
