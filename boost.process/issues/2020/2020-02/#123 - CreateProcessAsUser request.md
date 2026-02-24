# #123 - CreateProcessAsUser request [Open]

> Username: glumpo  
> Created at: 2020-02-25 11:36:36 UTC  
> Updated at: 2024-03-07 01:15:54 UTC  
> Url: https://github.com/boostorg/process/issues/123  

Is it possible to add support for user impersonation on windows? I guess its not really needed on other platforms, but on windows it would make life of number of developers much more happier.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2020-03-05 16:28:17 UTC  
> Url: https://github.com/boostorg/process/issues/123#issuecomment-595319999  

Do you have an idea how to implement that on posix?

---

## Comment 2

> Username: et-nik  
> Created at: 2020-04-15 17:12:43 UTC  
> Url: https://github.com/boostorg/process/issues/123#issuecomment-614165802  

Hello. I also need this feature.  
  
You can change the real and effective user id before calling `exec`.  
  
I think should call `setreuid(user_uid, user_uid);` before calling `::execve`.  
https://github.com/boostorg/process/blob/develop/include/boost/process/detail/posix/executor.hpp#L349  
  
Example:  
```cpp  
    this->pid = ::fork();  
    if (pid == -1)  
    {  
        auto ec = boost::process::detail::get_last_error();  
        boost::fusion::for_each(seq, call_on_fork_error(*this, ec));  
        return child();  
    }  
    else if (pid == 0)  
    {  
        // Change current user  
        ::setreuid(this->uid, this->euid);  
  
        boost::fusion::for_each(seq, call_on_exec_setup(*this));  
        ::execve(exe, cmd_line, env);  
        auto ec = boost::process::detail::get_last_error();  
        boost::fusion::for_each(seq, call_on_exec_error(*this, ec));  
        _exit(EXIT_FAILURE);  
    }  
```

---

## Comment 3

> Username: ReenigneArcher  
> Created at: 2024-03-07 00:56:44 UTC  
> Url: https://github.com/boostorg/process/issues/123#issuecomment-1982143828  

+1

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2024-03-07 01:15:53 UTC  
> Url: https://github.com/boostorg/process/issues/123#issuecomment-1982161514  

It's available in `boost::process::v2`.
