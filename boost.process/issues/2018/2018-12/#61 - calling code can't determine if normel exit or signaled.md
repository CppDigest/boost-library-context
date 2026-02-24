# #61 - calling code can't determine if normel exit or signaled [Closed]

> Username: olk  
> Created at: 2018-12-20 12:40:09 UTC  
> Updated at: 2019-01-08 14:31:52 UTC  
> Closed at: 2018-12-20 16:31:35 UTC  
> Url: https://github.com/boostorg/process/issues/61  

child::exit_code() evaluates WIFEXITED and WIFSIGNALED. Depending on that child::exit_code() returns WEXITSTATUS or WTERMSIG (see boost::process::detail::posix::eval_exit_status()).  
For code calling child::exit_code() it is not possible to determine if the returned value of `9` means that the forked process returned normally with exit-code `9` or the forked process was killed by SIGKILL (9 on Linux).

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-12-20 13:17:33 UTC  
> Url: https://github.com/boostorg/process/issues/61#issuecomment-448996650  

A function for that is in the develop-branch, where you can call `native_exit_code` to obtain the unevaluated exit-code. I'll have that in the master for the next release cycle.  
  
I don't think having that information included in the value returned by `exit_code()` would be the right choice, since that would be inconsistent with windows.

---

## Comment 2

> Username: olk  
> Created at: 2018-12-20 16:31:35 UTC  
> Url: https://github.com/boostorg/process/issues/61#issuecomment-449056982  

OK, ty

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2019-01-08 10:50:52 UTC  
> Url: https://github.com/boostorg/process/issues/61#issuecomment-452256626  

It's [there](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/child_decl.hpp#L104) - hope it works properly.

---

## Comment 4

> Username: olk  
> Created at: 2019-01-08 10:59:28 UTC  
> Updated at: 2019-01-08 14:31:52 UTC  
> Url: https://github.com/boostorg/process/issues/61#issuecomment-452258916  

sorry - forgot `git pull`🙄
