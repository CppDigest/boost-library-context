# #72 - co_main hangs for long-running, detached tasks [Closed]

> Username: anarthal  
> Created at: 2023-08-14 08:33:10 UTC  
> Updated at: 2023-09-05 00:57:26 UTC  
> Closed at: 2023-09-05 00:57:26 UTC  
> Url: https://github.com/boostorg/cobalt/issues/72  

If you spawn a long-running, detached task (like a websocket session), SIGINT won't cancel it, but `co_main` will wait for it, causing a deadlock.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-14 09:08:14 UTC  
> Url: https://github.com/boostorg/cobalt/issues/72#issuecomment-1676962666  

You `co_await` it ?

---

## Comment 2

> Username: anarthal  
> Created at: 2023-08-14 09:39:03 UTC  
> Url: https://github.com/boostorg/cobalt/issues/72#issuecomment-1677007154  

It's detached, so, no.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-08-14 10:03:55 UTC  
> Url: https://github.com/boostorg/cobalt/issues/72#issuecomment-1677041027  

That's by design then. SIGINT & SIGTERM send cancellation signals to main, not to everything.

---

## Comment 4

> Username: anarthal  
> Created at: 2023-08-14 15:27:01 UTC  
> Url: https://github.com/boostorg/cobalt/issues/72#issuecomment-1677549977  

But `co_main` *does* wait for all tasks to finish, detached or not. That's calling for deadlocks.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2023-08-16 02:06:21 UTC  
> Url: https://github.com/boostorg/cobalt/issues/72#issuecomment-1679859791  

I'll check if I can wire up detached tasks to a global cancellation thing so they can get signalled.  
  
My idea was that you don't detach, but use wait_group et al.

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2023-09-05 00:57:26 UTC  
> Url: https://github.com/boostorg/cobalt/issues/72#issuecomment-1705806856  

I think the approach is correct. It's akin to a `main` creating & detaching threads, which won't be stopped.
