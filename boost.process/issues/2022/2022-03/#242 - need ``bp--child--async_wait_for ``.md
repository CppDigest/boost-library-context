# #242 - need  ``bp::child::async_wait_for `` [Closed]

> Username: microcai  
> Created at: 2022-03-14 17:15:49 UTC  
> Updated at: 2022-06-11 06:39:25 UTC  
> Closed at: 2022-06-11 06:39:25 UTC  
> Url: https://github.com/boostorg/process/issues/242  

for windows platform, asio provide async_wait  on [windows::object_handle](https://www.boost.org/doc/libs/master/doc/html/boost_asio/reference/windows__object_handle.html)  
  
for linux platform, pidfd_open can return an pollable fd from child pid that can then be async_wait by asio::posix::basic_filedescriptor  
  
for other platform,  thread helped waitid can be deployed to simulate async operations.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-06-11 06:39:25 UTC  
> Url: https://github.com/boostorg/process/issues/242#issuecomment-1152868079  

Async_wait is in v2, and can be combined with make_parallel_group to have a timeout.
