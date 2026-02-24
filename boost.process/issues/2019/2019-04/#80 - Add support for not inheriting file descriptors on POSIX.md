# #80 - Add support for not inheriting file descriptors on POSIX [Open]

> Username: Lastique  
> Created at: 2019-04-25 13:13:09 UTC  
> Updated at: 2019-04-25 13:37:29 UTC  
> Url: https://github.com/boostorg/process/issues/80  

I'd like to ask to support non-inheriting file descriptors in child processes on POSIX systems, in particular, on Linux.  
  
Rationale:  
This is an often desired feature, that is already available in Windows via `inherit_handles` flag. Although it can be implemented by user as `on_exec_setup` handler, I believe, it is common and tricky enough to benefit from being part of Boost.Process out of the box.  
  
Note that I'm not considering marking all file descriptors with `CLOEXEC` flags in the user's code because that is not practical. Most third party libraries that open file descriptors, including the C/C++ standard libraries (e.g. `std::FILE`, iostreams) do not set `CLOEXEC` flags and there is no way to force all file descriptors to automatically have that flag process-wide.  
  
Possible implementation:  
Since POSIX does not define an API to not inherit file descriptors on `exec`, one suggestion is to do iterate over file descriptors, available in `/proc/self/fd`, in the child process after `fork` and before `exec`, and closing them. Note that file descriptors 0, 1 and 2 need to be skipped from closing, as these are stdin, stdout and stderr. These fds are also available as `STDIN_FILENO`, `STDOUT_FILENO` and `STDERR_FILENO` constants, which could be used instead of integers.  
  
This functionality should be made optional, controllable by a flag (`inherit_handles` makes a precedent here).  
  
This request comes from this Boost.Log ticket: https://github.com/boostorg/log/issues/83.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2019-04-25 13:21:52 UTC  
> Url: https://github.com/boostorg/process/issues/80#issuecomment-486670181  

I actually have a PR for that, it's a long requested features - could you check if https://github.com/klemens-morgenstern/boost-process/pull/196 does the job?

---

## Comment 2

> Username: Lastique  
> Created at: 2019-04-25 13:37:29 UTC  
> Url: https://github.com/boostorg/process/issues/80#issuecomment-486675953  

I'm not familiar with Boost.Process implementation, but if it does what is requested, great! Thanks.
