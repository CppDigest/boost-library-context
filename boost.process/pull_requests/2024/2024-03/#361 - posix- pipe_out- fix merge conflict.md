# #361 posix: pipe_out: fix merge conflict [Merged]

> Username: ceggers-arri  
> Created at: 2024-03-27 17:32:00 UTC  
> Updated at: 2024-03-28 13:56:59 UTC  
> Merged at: 2024-03-28 13:56:58 UTC  
> Closed at: 2024-03-28 13:56:58 UTC  
> Url: https://github.com/boostorg/process/pull/361  

In commit cbaa913e3d73 ("Merge branch 'develop' into limit_fd"), there have been merge conflicts in two files. In pipe_out.hpp, a previous commit from the "limit_fd" branch f8c0dd4da58a ("prototype for limit_fd") had been eliminated during wrong conflict resolution.  
  
The final result was, that file descriptors for stdout pipes were not preserved when using limit_handles.  
  
Example:  
  
boost::asio::io_context io_context;  
bp::async_pipe my_stdin(io_context);  
bp::async_pipe my_stdout(io_context);  
bp::child my_child("/usr/bin/echo", "Hello world",  
    bp::std_in  < my_stdin,  // preserved by limit_handles  
    bp::std_out > my_stdout, // closed by limit_handles  
    bp::std_err > stderr,    // preserved by limit_handles  
    bp::limit_handles)  
  
Fixes: cbaa913e3d73 ("Merge branch 'develop' into limit_fd")

---

## Comment 1

> Username: ceggers-arri  
> Created_at: 2024-03-28 06:39:30 UTC  
> Url: https://github.com/boostorg/process/pull/361#issuecomment-2024504365  

Just as a personal reminder how to show the **full** history of a file in Git:  
  
- https://stackoverflow.com/a/56556222/23126847  
- https://git-scm.com/docs/git-log#_history_simplification  
- `git log --full-history --simplify-merges --patch --diff-merges=on -- include/boost/process/detail/posix/pipe_out.hpp`

---
