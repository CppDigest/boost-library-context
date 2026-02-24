# #103 Fix deallocating never-allocated storage in vector.merge() [Merged]

> Username: rolandd  
> Created at: 2019-02-23 22:16:28 UTC  
> Updated at: 2019-02-26 23:15:57 UTC  
> Merged at: 2019-02-26 23:15:47 UTC  
> Closed at: 2019-02-26 23:15:47 UTC  
> Url: https://github.com/boostorg/container/pull/103  

If merge() is called on an empty vector, then priv_merge_in_new_buffer() will  
call deallocate() with size 0 on the old (not-yet-allocated) vector storage.  
This violates the Allocator requirement that the pointer passed to deallocate()  
must have come from a call to allocate() with the same size.  
  
Fix this by checking old_cap against 0, and also add a unit test for this bug.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2019-02-26 23:15:57 UTC  
> Url: https://github.com/boostorg/container/pull/103#issuecomment-467654980  

Many thanks for the patch.

---
