# #337 Reap zombie after failed execve. Issue 285  [Closed]

> Username: SilverPlate3  
> Created at: 2023-08-21 05:27:55 UTC  
> Updated at: 2023-12-16 17:05:41 UTC  
> Closed at: 2023-12-16 17:05:41 UTC  
> Url: https://github.com/boostorg/process/pull/337  

After @klemens-morgenstern logical comments on the latest [pull request](https://github.com/boostorg/process/pull/325).  
I created this pull request and closed the previous one.  
  
In order to fix the [issue #217](https://github.com/boostorg/process/issues/285) this pull request does:  
1 - Extracts ` _set_error()` out of `_read_error()`.  The function  `_set_error()` throws an exception which causes the code to never get to the `waitpid()` that cleans a zombie process after it failed an execve.   
Now `_read_error()` only reads the error as its name implies, and `_set_error()` will be called only after the `waitpid()`  
  
2 - After the first step, I faced a race condition between the `::waitpid(this->pid, nullptr, WNOHANG);` and the actual `_exit(EXIT_FAILURE);` of the process which failed the execve. The `::waitpid(this->pid, nullptr, WNOHANG);` was called some times before the `_exit(EXIT_FAILURE);`.  
In order to fix this, I check what was the failure reason that I get from `_read_error(p.p[0]);` and set the `waitpid()` parameter according to it.   
  
3 - Added a unit test to check that the fix works.

---

## Review 1 [Commented]

> Username: klemens-morgenstern  
> Created_at: 2023-08-21 05:31:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/process/pull/337#pullrequestreview-1586211447  

📁 include/boost/process/detail/posix/executor.hpp

```diff
 455 |         //if an error occurred we need to reap the child process
 448 |-         ::waitpid(this->pid, nullptr, WNOHANG);
 456 |+         int block_mode = _msg == "execve failed" ? 0 : WNOHANG;
```

> Username: klemens-morgenstern  
> Created_at: 2023-08-21 05:30:41 UTC  
> Updated_at: 2023-08-21 05:31:40 UTC  
> Url: https://github.com/boostorg/process/pull/337#discussion_r1299614702  

Why is this conditional?

> Username: SilverPlate3  
> Created_at: 2023-08-22 19:09:46 UTC  
> Url: https://github.com/boostorg/process/pull/337#discussion_r1302079195  

Great question.   
**First we must know**:  
Doesn't Block - `::waitpid(this->pid, nullptr, WNOHANG); `  
Blocks until it has a child zombie to reap - `::waitpid(this->pid, nullptr, 0); `  
  
We don't always want to block. For example, if we look in `_read_error()`, there are several things that can assign a value to `_ec`. One of them is a failed `::read()`. Failed `::read()` doesn't ensure us that we have a zombie child to reap. We want to use  `::waitpid(this->pid, nullptr, 0); ` only when we are sure we have a zombie to reap so we won't be blocked for a long time.  
Only if `_msg == "execve failed"` we know the child has exited or will exit in its next few lines.

> Username: klemens-morgenstern  
> Created_at: 2023-08-25 06:23:19 UTC  
> Updated_at: 2023-08-25 06:23:44 UTC  
> Url: https://github.com/boostorg/process/pull/337#discussion_r1305223489  

I don't think that's correct. The closed pipe would indicate that the child process exited already, so WNOHANG should be correct.

> Username: klemens-morgenstern  
> Created_at: 2023-08-25 06:24:39 UTC  
> Url: https://github.com/boostorg/process/pull/337#discussion_r1305224480  

And comparing error messages is just bad practice. Why can't we just wait and ignore the error if the child doesn't exists?

---

📁 include/boost/process/detail/posix/executor.hpp

```diff
 457 |+         ::waitpid(this->pid, nullptr, block_mode);
 458 |         boost::fusion::for_each(seq, call_on_error(*this, _ec));
 459 |+         set_error(_ec, _msg);
```

> Username: klemens-morgenstern  
> Created_at: 2023-08-21 05:31:03 UTC  
> Updated_at: 2023-08-21 05:31:40 UTC  
> Url: https://github.com/boostorg/process/pull/337#discussion_r1299614909  

what is this for? we already reported the error.

> Username: SilverPlate3  
> Created_at: 2023-08-22 19:20:36 UTC  
> Url: https://github.com/boostorg/process/pull/337#discussion_r1302093001  

Indeed.   
  
However, on top of setting `_ec` and `_msg`, `set_error()` indirectly calls `throw process_error(ec, msg); `  
Because I have extracted `set_error()` out of `_read_error() ` and because `throw process_error` is only called in error related functions in executor.hpp I thought it will be most appropriate.   
  
Would you prefer me changing this `set_error(_ec, _msg);` to `throw process_error(_ec, _msg); ` ?

> Username: klemens-morgenstern  
> Created_at: 2023-08-25 06:23:38 UTC  
> Updated_at: 2023-08-25 06:23:44 UTC  
> Url: https://github.com/boostorg/process/pull/337#discussion_r1305223719  

Neither is correct I fear.


---

## Comment 2

> Username: SilverPlate3  
> Created_at: 2023-08-25 06:20:17 UTC  
> Url: https://github.com/boostorg/process/pull/337#issuecomment-1692827591  

@klemens-morgenstern Hey.  
When can I expect this to be merged? Or a respond on the pull request comments?  
Thanks you very much!

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2023-10-05 02:51:48 UTC  
> Url: https://github.com/boostorg/process/pull/337#issuecomment-1747957518  

Can you check if 13c4b0aa144714007bfe26ab1a5db2e0f1519011 solves the issue?

---
