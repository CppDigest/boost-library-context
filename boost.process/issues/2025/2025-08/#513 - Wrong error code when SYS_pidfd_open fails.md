# #513 - Wrong error code when SYS_pidfd_open fails [Closed]

> Username: raffopazzo  
> Created at: 2025-08-30 14:03:23 UTC  
> Updated at: 2025-10-01 20:53:43 UTC  
> Closed at: 2025-09-07 09:45:18 UTC  
> Url: https://github.com/boostorg/process/issues/513  

When running inside a docker container without the `--privileged` flag, `SYS_pidfd_open` from the link below fails with _Permission Error_ but the return code is not checked; it is later on passed to `epoll_ctl()` with `EPOLL_CTL_ADD` which fails with _Bad File Descriptor_ and it is this error code that is returned to the user, causing a bit of confusion. It would be more helpful if the _Permission Error_ was returned instead, as that would have helped me find the issue with docker more quickly.   
  
https://github.com/boostorg/process/blob/5597aa0055b81017f6ea24cb652f07a014b12771/include/boost/process/v2/detail/process_handle_fd.hpp#L70  
  
You should be able to reproduce with something as simple as  
```cpp  
    auto const ls = boost::process::v2::environment::find_executable("ls");  
    boost::asio::io_context ctx;  
    boost::process::v2::process proc(ctx, ls, args);  
    proc_result = proc.wait();  
```  
and run it from inside a docker container without privileges

---

## Comment 1

> Username: rhapsodyv  
> Created at: 2025-10-01 20:53:43 UTC  
> Url: https://github.com/boostorg/process/issues/513#issuecomment-3358096839  

Man, I lost some hours hunting a `bad file descriptor` issue... but this was the real issue!  For me the issue only happened in a  orange pi running a x86 binary with qemu-user-static, so it was a little hard to debug. But setting `#define BOOST_PROCESS_V2_DISABLE_PIDFD_OPEN` solved my issues!   
Thanks!
