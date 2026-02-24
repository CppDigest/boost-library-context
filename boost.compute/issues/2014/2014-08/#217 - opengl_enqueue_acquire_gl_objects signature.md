# #217 - opengl_enqueue_acquire_gl_objects signature [Closed]

> Username: f-koehler  
> Created at: 2014-08-07 21:05:45 UTC  
> Updated at: 2014-08-10 09:22:19 UTC  
> Closed at: 2014-08-10 09:22:19 UTC  
> Url: https://github.com/boostorg/compute/issues/217  

When working on issue [#216](https://github.com/kylelutz/compute/issues/216) I was wondering why the signature of `opengl_enqueue_acquire_gl_objects` is different from other enqueue methods (see `command_queue.hpp`). One could easily give it a wait_list as well and return an event.  
  
Is it because this method is not async?

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-08-08 01:53:32 UTC  
> Url: https://github.com/boostorg/compute/issues/217#issuecomment-51555037  

Just because I haven't found time to update them yet ;-).  
  
If you want to submit a pull-request with the changes for the `opengl_enqueue_acquire_*` functions I'll merge it in (should be roughly the same pattern as the other `enqueue_*` methods in `command_queue`).

---

## Comment 2

> Username: f-koehler  
> Created at: 2014-08-08 17:16:54 UTC  
> Url: https://github.com/boostorg/compute/issues/217#issuecomment-51631274  

See PR [#225](https://github.com/kylelutz/compute/pull/225)

---

## Comment 3

> Username: f-koehler  
> Created at: 2014-08-10 09:22:19 UTC  
> Url: https://github.com/boostorg/compute/issues/217#issuecomment-51710086  

Fixed in PR [#225](https://github.com/kylelutz/compute/pull/225)
