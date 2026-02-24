# #503 - [v2] terminal cancellation on async_execute() results in exit status 0 instead of 9 [Closed]

> Username: pgit  
> Created at: 2025-06-30 20:02:17 UTC  
> Updated at: 2025-07-01 06:26:32 UTC  
> Closed at: 2025-07-01 06:16:53 UTC  
> Url: https://github.com/boostorg/process/issues/503  

When calling `terminate` on a V2 process, the exit code on `async_wait()` is 0, but should be 9 (SIGKILL).  
  
````c++  
#include <boost/asio.hpp>  
#include <boost/process/v2/process.hpp>  
#include <iostream>  
  
int main()  
{  
   boost::asio::io_context context;  
   boost::process::v2::process child(context, "/usr/bin/sleep", {"10"});  
   child.async_wait([](boost::system::error_code ec, int status){  
      std::cout << status << std::endl;  
   });  
   child.terminate();  // prints 0 -- should be 9 (SIGKILL)  
   // child.interrupt();  // prints 2 (SIGINT)  
   // child.request_exit();  // prints 15 (SIGTERM)  
   context.run();  
}  
````  
  
This seems to be due to the implementation of terminate(), which -- unlike `interrupt()` and `request_exit` -- also calls `wait`:  
https://github.com/boostorg/process/blob/bd450f98315e42976b2f00f8627e249cf8ee3874/include/boost/process/v2/detail/process_handle_signal.hpp#L239  
  
That wait() correctly retireves exit status 9, but before `async_execute()` completes, `::waitpid()` is invoked again somewhere. If the `wait()` is removed, `termiante()` behaves correctly -- but I do not know if that has other implications.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-07-01 02:04:51 UTC  
> Url: https://github.com/boostorg/process/issues/503#issuecomment-3021447895  

Yes, terminate waits, so that the process is removed from the process table from the process table after `terminate`.   
  
This should be caught by async_wait however, so you're reporting a bug.

---

## Comment 2

> Username: pgit  
> Created at: 2025-07-01 06:26:32 UTC  
> Url: https://github.com/boostorg/process/issues/503#issuecomment-3022027350  

Didn't verify, but your testcase is essentially the same as the sample above. Thank you for the quick reaction.
