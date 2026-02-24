# #197 use post() instead dispatch() for async_await() [Closed]

> Username: olk  
> Created at: 2021-01-22 09:53:14 UTC  
> Updated at: 2021-10-14 06:48:32 UTC  
> Closed at: 2021-10-14 06:48:32 UTC  
> Url: https://github.com/boostorg/process/pull/197  

- dispatch() might queue the completion handler or invoke it immediately  
- this might result in problems if this code runs in  completion  
  handlers like coroutines where you must **not** resume an already running coroutine  
- using post() solves this issue

---

## Comment 1

> Username: gulachek  
> Created_at: 2021-01-24 02:39:19 UTC  
> Url: https://github.com/boostorg/process/pull/197#issuecomment-766279985  

This might fix another issue you found, but this documented issue (196) isn't fixed by the diff I'm seeing.  If you see the AsyncReadStream requirements, the async_pipe needs to implement a get_executor member function.

---

## Comment 2

> Username: olk  
> Created_at: 2021-01-24 07:00:35 UTC  
> Updated_at: 2021-01-24 08:53:20 UTC  
> Url: https://github.com/boostorg/process/pull/197#issuecomment-766302602  

This patch from above seams to solve our production code.  
  
The problem was that async_coroutine::push() was invoked from sigchld_service::async_wait() while the stackful coroutine was already running.  
  
I refer to the issue #195  not pull-request #196 - my mistake, sorry.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2021-10-09 08:24:50 UTC  
> Url: https://github.com/boostorg/process/pull/197#issuecomment-939255470  

I think the outer one should be dispatch, but the inner one should post.

---

## Comment 4

> Username: olk  
> Created_at: 2021-10-09 08:59:06 UTC  
> Updated_at: 2021-10-09 09:18:02 UTC  
> Url: https://github.com/boostorg/process/pull/197#issuecomment-939260412  

Klemens, I'll try your suggestion at our production code...  
But could you give me an explanation why you think that the outer one should call `dispatch()`?

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2021-10-09 09:19:20 UTC  
> Url: https://github.com/boostorg/process/pull/197#issuecomment-939263491  

The outer just needs to make sure it's on the right executor. I actually think the fix would be to post these invocations:of `h`.  
  
```cpp  
                    if (pid_res < 0)  
                        h(-1, get_last_error());  
                    else if ((pid_res == pid) && (WIFEXITED(status) || WIFSIGNALED(status)))  
                        h(status, {}); //successfully exited already  
```

---
