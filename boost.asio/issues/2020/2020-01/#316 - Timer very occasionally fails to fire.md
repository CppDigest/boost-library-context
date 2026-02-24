# #316 - Timer very occasionally fails to fire [Closed]

> Username: bmerry  
> Created at: 2020-01-08 15:16:41 UTC  
> Updated at: 2020-12-30 01:12:25 UTC  
> Closed at: 2020-12-30 01:12:24 UTC  
> Url: https://github.com/boostorg/asio/issues/316  

We're running into a problem where about once a month a system in production will freeze up. Unfortunately that means I don't have an easily reproducible example, but any suggestions on what to inspect next time it happens will be welcome.  
  
Today I was able to attach gdb to investigate. It appears that we're never getting the wakeup callback from a timer. Poking at the internals, I can see the timer in the `timer_queues_` of the `epoll_reactor`, the expiry time is in the past, and if I call `get_timeout` on the epoll_reactor it returns zero (because there is a live timer with an expiry in the past).  
  
What's also odd is that /proc/<pid>/fdinfo/<timerfd> showed this:  
```  
pos:	0  
flags:	02000002  
mnt_id:	13  
clockid: 1  
ticks: 1  
settime flags: 00  
it_value: (0, 0)  
it_interval: (0, 0)  
```  
  
If I'm interpreting that correctly, the timer has fired (`ticks: 1`) and is no longer armed. That suggests that `epoll_wait` is for some reason not being woken up (maybe somehow related to edge triggering?) I'm sure there is a thread running `epoll_wait` because that's the thread I interrupted to start inspecting data structures.  
  
Here's the fdinfo for the epollfd, in case it helps:  
```  
pos:	0  
flags:	02000002  
mnt_id:	13  
tfd:       29 events: 80000019 data:          3202a68  pos:0 ino:2b6e sdev:d  
tfd:       31 events:       19 data:          3202a74  pos:0 ino:2b6e sdev:d  
tfd:       32 events: 8000001b data:          317fc50  pos:0 ino:3e004571 sdev:9  
```  
  
The application design is that each `io_service` is only being run by one thread (although there are multiple instances of `io_services`), the timer in question is only armed by that thread, and only armed again once the callback has fired (it is of course possible that I've screwed it up). The timer is fired multiple times a second, so a hang every month or so suggests that it may be an extremely rare race condition (and I've been unable to provoke it with a short run of a timer stress test).  
  
System information:  
- Ubuntu 16.04.4 host  
- Linux 4.13.0-41-generic  
- Ubuntu 18.04-based Docker container  
- Boost 1.70.0 (I know it's no longer the latest - let me know if there are possibly relevant fixes in newer versions).

---

## Comment 1

> Username: bmerry  
> Created at: 2020-01-09 13:47:22 UTC  
> Url: https://github.com/boostorg/asio/issues/316#issuecomment-572568718  

I see that the timerfd is level-triggered. So the only way I can see for this situation to arise is that either `epoll_wait` isn't currently being called (which I really thought it was, but I'll have to double-check next time this happens), or a kernel bug is preventing the timer event from being delivered to wake up `epoll_wait`.

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:12:23 UTC  
> Url: https://github.com/boostorg/asio/issues/316#issuecomment-752293353  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#740](https://github.com/chriskohlhoff/asio/issues/740).
