# #15 Fix double delete in new_delete_allocator [Merged]

> Username: EricWF  
> Created at: 2016-08-30 04:50:45 UTC  
> Updated at: 2016-08-30 05:02:12 UTC  
> Merged at: 2016-08-30 05:00:32 UTC  
> Closed at: 2016-08-30 05:00:32 UTC  
> Url: https://github.com/boostorg/pool/pull/15  

The `deallocate` method of `new_delete_allocator` currently invokes the destructor on the supplied pointer. To prevent this it needs to be cast back to a `char*` first.

---
