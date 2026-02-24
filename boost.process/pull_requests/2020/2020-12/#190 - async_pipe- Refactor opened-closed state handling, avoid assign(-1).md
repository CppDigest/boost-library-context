# #190 async_pipe: Refactor opened/closed state handling, avoid assign(-1) [Closed]

> Username: dkl  
> Created at: 2020-12-08 21:40:49 UTC  
> Updated at: 2021-10-14 06:36:23 UTC  
> Closed at: 2021-10-14 06:36:23 UTC  
> Url: https://github.com/boostorg/process/pull/190  

The async_pipe move_pipe unit test failed (here on Linux):  
```  
fatal error: in "async/move_pipe": Throw location unknown (consider using BOOST_THROW_EXCEPTION)  
Dynamic exception type: boost::wrapexcept<boost::system::system_error>  
std::exception::what: assign: Bad file descriptor  
```  
  
This was caused by the `stream_descriptor.assign(-1)` calls in the async_pipe move constructor. strace shows that it's trying to call epoll_ctl(EPOLL_CTL_ADD) for the -1 fd, which fails with EBADF.  
  
I don't know whether maybe this used to work with older asio versions, or on different systems, but either way, checking the opened/closed state instead of manually setting/checking for -1 seems like a good solution.  
  
This patch also fixes some potential fd leaks from some open() and dup() calls in case of exceptions.

---
