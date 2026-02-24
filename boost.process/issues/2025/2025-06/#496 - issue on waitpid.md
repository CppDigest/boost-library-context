# #496 - issue on waitpid [Open]

> Username: jean-christophe81  
> Created at: 2025-06-19 08:21:42 UTC  
> Updated at: 2025-07-09 19:13:52 UTC  
> Url: https://github.com/boostorg/process/issues/496  

boost version 1.88  
  
I use process v2 on linux  
My io_context is run by 4 threads  
  
I have a child process running and I have started an async_wait.  
I kill child process, then inside terminate, waitpid is called  
On async_wait completion, waitpid is also called and waitpid returns ECHILD what go to this error: "No child processes"

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-06-20 16:37:22 UTC  
> Url: https://github.com/boostorg/process/issues/496#issuecomment-2992218204  

Can you check if the ECHILD check solves the issue?

---

## Comment 2

> Username: pgit  
> Created at: 2025-07-09 19:13:52 UTC  
> Url: https://github.com/boostorg/process/issues/496#issuecomment-3053730657  

Closely related: #503, which is also about `terminate()` doing `waitpid()`.  
I also ran into this issue and can confirm that f9c13ff fixes this for me.
