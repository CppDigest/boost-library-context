# #140 - detail::exec_op::info memory not freed before completion [Closed]

> Username: anarthal  
> Created at: 2023-08-15 10:27:23 UTC  
> Updated at: 2025-06-10 11:24:42 UTC  
> Closed at: 2025-06-10 11:24:42 UTC  
> Url: https://github.com/boostorg/redis/issues/140  

Memory for `req_info_type` in `exec_op` is being allocated using the handler's associated allocator, but is not being freed before calling `complete`. This is a violation of one of Asio's composed ops principles.  
  
This has been caught by ubsan when using Boost.Redis together with Boost.Async (I'm exercising the library by rewriting BoostServerTech). Boost.Async uses an internal, per-operation allocator. That is, when you call `conn.async_exec(whatever, use_op)`, a stack-based memory pool of 2kb is created. All allocations for this `async_exec` will use these 2kb of memory first. Once this `async_exec` operation is done, the pool is destroyed. However, the call `self.complete` within `async_exec` resumes and keeps executing the coroutine that called `async_exec`. At this point, `async_exec` has a `req_info_type` object pointing to memory that has been freed (or re-used). When the `shared_ptr<req_info_type>` destructor runs, it will be accessing freed memory.  
  
It's likely that this can be used to build exploits leading to arbitrary code execution.

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-08-31 10:41:04 UTC  
> Url: https://github.com/boostorg/redis/issues/140#issuecomment-1700796837  

This could be fixed after my last merge on develop where I remove the use of associated allocator.

---

## Comment 2

> Username: anarthal  
> Created at: 2023-08-31 13:19:11 UTC  
> Url: https://github.com/boostorg/redis/issues/140#issuecomment-1701026019  

Even if you use the standard allocator, I'd say you should free any temporary memory acquired by the operation before calling complete, unless there is a strong reason not to do so, so that allocations don't stack up. But probably Klemens can give you a better understanding of the subject.

---

## Comment 3

> Username: anarthal  
> Created at: 2025-06-10 11:24:42 UTC  
> Url: https://github.com/boostorg/redis/issues/140#issuecomment-2958784683  

This is no longer a problem since the allocations of multiplexer::elem use the standard allocator, and are no longer considered transient. This is, under certain circumstances (e.g. we get a cancellation and we must leave the elem allocated after invoking the handler). #250 will make this better, but still, allocations are non-transient at this point, so this issue no longer makes sense.
