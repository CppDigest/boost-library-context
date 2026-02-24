# #496 Return events from all command_queue::enqueue_* methods [Merged]

> Username: jszuppe  
> Created at: 2015-08-16 15:22:41 UTC  
> Updated at: 2016-05-01 11:19:21 UTC  
> Merged at: 2015-09-02 04:49:23 UTC  
> Closed at: 2015-09-02 04:49:23 UTC  
> Url: https://github.com/boostorg/compute/pull/496  

This resolves https://github.com/boostorg/compute/issues/365. However, there're two methods that needed workarounds:  
1. `clEnqueueBarrier(...)`  in OpenCL 1.0 and 1.1 does not take event argument, so obviously event object cannot be returned in universal `enqueue_barrier(void)` method, but there is already `event enqueue_barrier(const wait_list &events)` method so user can just call it with empty list of events to get event associated with enqueued barrier.   
2. I think that in mapping methods like `enqueue_map_buffer` it's better to return pointer to mapped region like it's now. Therefore, instead of modifying them to return an event object I added event argument to these methods.  
  
I also added missing enqueue_map_image method and asynchronous version of a few other `command_queue` methods.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-08-18 15:48:03 UTC  
> Url: https://github.com/boostorg/compute/pull/496#issuecomment-132255646  

Looks good. I need to think about the new function signatures a little bit.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2015-08-19 11:00:02 UTC  
> Url: https://github.com/boostorg/compute/pull/496#issuecomment-132540581  

Ok. I can move last two commits to another pull-request if you're ready to accept  https://github.com/haahh/compute/commit/9ec5dcaca56f3b13013356b3a74b8a859313da07 (first commit) which resolves https://github.com/boostorg/compute/issues/365.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2015-08-20 04:14:32 UTC  
> Url: https://github.com/boostorg/compute/pull/496#issuecomment-132882218  

Yeah, let's do that.  
  
My main concern is the new `const map_buffer_event&` arguments which are actually written to (as an output argument) event though they are `const`. Perhaps this would be better as either a pointer defaulted to `0` or have separate overloads and accept a non-const reference. I'm not sure though. I'll think more about it.

---

## Comment 4

> Username: jszuppe  
> Created_at: 2015-08-20 10:15:18 UTC  
> Url: https://github.com/boostorg/compute/pull/496#issuecomment-132965310  

Yeah, you're right. This change is already introduced in the first commit https://github.com/haahh/compute/commit/9ec5dcaca56f3b13013356b3a74b8a859313da07 - `const event &event` argument was added to `enqueue_map_buffer` function (in next commit I renamed it to `const event &map_buffer_event` and moved before `const wait_list &events` argument). Second point describes why I did this that way. Now this change is gone.  
  
I lean toward the solution with a non-const reference (both for synchronous and asynchronous methods).   
  
``` cpp  
    void* enqueue_map_buffer(const buffer &buffer,  
                             cl_map_flags flags,  
                             size_t offset,  
                             size_t size,  
                             event& map_buffer_event,  
                             const wait_list &events = wait_list())  
    {  
        ...  
    }  
  
    ///\overload  
    void* enqueue_map_buffer(const buffer &buffer,  
                             cl_map_flags flags,  
                             size_t offset,  
                             size_t size,  
                             const wait_list &events = wait_list())  
    {  
        event event_;  
        return enqueue_map_buffer(buffer, flags, offset, size, event_, events);   
    }  
```

---
