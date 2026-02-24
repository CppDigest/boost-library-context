# #350 Fix service_registry::do_use_service blocking forever if a service with the same key was created [Closed]

> Username: Erlkoenig90  
> Created at: 2020-05-11 09:49:49 UTC  
> Updated at: 2020-12-30 01:24:41 UTC  
> Closed at: 2020-12-30 01:24:41 UTC  
> Url: https://github.com/boostorg/asio/pull/350  

The `service_registry::do_use_service` function can hang forever on Windows if a service with the same key was created and registered while the mutex was unlocked. If the service uses `win_iocp_io_context`, its destructor (implicitly called upon return, by destroying `new_service`) will `join()` the internal thread for IOCP handling, without requesting it to stop before. This will leave both threads to block forever, while the `mutex_` is still locked (`lock`s destructor may be called _after_ `new_service`s), which may cause further blockages in other threads. This PR aims to fix this by calling `shutdown()` on the thread before returning, and unlocking the mutex explicitly before that. Another solution would be to call `shutdown()` from within `win_iocp_io_context::~win_iocp_io_context()`.

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2020-12-30 01:24:41 UTC  
> Url: https://github.com/boostorg/asio/pull/350#issuecomment-752295357  

Fixed in asio 1.17.0 / boost 1.74 by making the `win_iocp_io_context` destructor clean up correctly even if the `shutdown` function is not run.

---
