# #364 posix: Fix pipe fd leak if redirecting both stdout and stderr [Merged]

> Username: dkl  
> Created at: 2024-04-17 15:04:49 UTC  
> Updated at: 2024-06-04 00:54:21 UTC  
> Merged at: 2024-06-04 00:54:21 UTC  
> Closed at: 2024-06-04 00:54:21 UTC  
> Url: https://github.com/boostorg/process/pull/364  

Hi,  
  
This pull request re-adds a close() call in posix pipe_out, which already existed before commit caa7b2fcc8. pipe_out should always close its fd for the read end of the pipe. It already did it when redirecting only stdout or only stderr, but it was missing for the third case where both stdout and stderr are redirected (`bp::std_out & bp::std_err > my_async_pipe`).  
  
The second commit adds a unit test for this issue, hopefully it is useful, although I'm not sure if this style of test is okay.

---
