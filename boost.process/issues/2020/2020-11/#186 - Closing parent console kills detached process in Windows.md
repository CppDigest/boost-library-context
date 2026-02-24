# #186 - Closing parent console kills detached process in Windows [Open]

> Username: giovannipaganini95  
> Created at: 2020-11-30 22:54:18 UTC  
> Updated at: 2023-07-06 06:12:45 UTC  
> Url: https://github.com/boostorg/process/issues/186  

I've noticed a strange behaviour when i'm running a "launcher" that execute the following code:  
```  
#include <boost/process.hpp>  
#include <boost/process/child.hpp>  
  
namespace bp = boost::process;  
  
bp::child c("./client_service");  
c.detach();  
return;  
```  
The child process starts correctly and is kept alive even if the main process returns. But, as soon as the Windows console is closed, the child process dies. This problem occurs only in Windows, while in Linux the code works like a charm. This also occurs using spawn instead of child+detach.  
Digging a little bit, this appers related to the [Windows  Process Creation Flags](https://docs.microsoft.com/en-us/windows/win32/procthread/process-creation-flags) and the standard parent console inherit fashion. In particular, passing 0 as creation flag to the CreateProcess, makes the child inherit the parent console and so, when it is closed, the child is terminated by the OS. Instead, passing "DETACHED_PROCESS" (0x08),  the new process does not inherit its parent's console and continues after cmd is closed. Is this behaviour wanted? Or is it not supposed to happen?

---

## Comment 1

> Username: ArielSilver  
> Created at: 2023-07-06 06:11:30 UTC  
> Url: https://github.com/boostorg/process/issues/186#issuecomment-1623052432  

@klemens-morgenstern   
Can you please verify if its intended or not?  
If not I will create a Pull Request and fix it.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-07-06 06:12:45 UTC  
> Url: https://github.com/boostorg/process/issues/186#issuecomment-1623053473  

That is intended as it is the default behaviour on the os
