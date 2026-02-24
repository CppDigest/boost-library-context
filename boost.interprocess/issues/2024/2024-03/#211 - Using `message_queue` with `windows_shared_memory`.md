# #211 - Using `message_queue` with `windows_shared_memory` [Open]

> Username: Nerixyz  
> Created at: 2024-03-03 10:03:39 UTC  
> Updated at: 2024-03-03 10:03:39 UTC  
> Url: https://github.com/boostorg/interprocess/issues/211  

We're currently using `message_queue` on Windows, but want to avoid files being created on the user's filesystem (even in the application's config/cache directory). Moreover, we'd like to have the semantics of `windows_shared_memory` (mostly the destruction once all handles to the memory are closed).  
  
Currently, `message_queue` uses `shared_memory_object`. Would it be possible for users to specify the underlying abstraction (e.g. `message_queue_t<offset_ptr<void>, windows_shared_memory>`)?  
  
I experimented a bit by using `managed_open_or_create_impl<windows_shared_memory, 0, false, false>`, which worked fine for me (I might have missed some bugs as I don't know IPC too well). One downside is that `message_queue_t::remove` can't be used with `windows_shared_memory`, as it doesn't provide that functionality (the documentation for `~message_queue_t` should be updated, too).  
  
I'd be fine if this was done via a `#define`, but it would be nicer if an approach similar to the previously mentioned could be taken.  
If this is something that could get included in the library, I'd be fine with implementing it.
