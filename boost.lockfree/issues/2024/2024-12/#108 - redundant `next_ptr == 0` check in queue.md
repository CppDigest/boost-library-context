# #108 - redundant `next_ptr == 0` check in queue? [Open]

> Username: copyrat90  
> Created at: 2024-12-26 01:26:57 UTC  
> Updated at: 2024-12-26 01:26:57 UTC  
> Url: https://github.com/boostorg/lockfree/issues/108  

I really don't see why this check is required:  
  
https://github.com/boostorg/lockfree/blob/ba92d679232e08bd4d1b98766118acfba712627b/include/boost/lockfree/queue.hpp#L458-L464  
  
The comment says it clears the next part during allocation, however looking at `queue::node`'s constructor, it seems that it doesn't clear the tag;  
Rather, it increases the tag to avoid ABA problem.  
  
https://github.com/boostorg/lockfree/blob/ba92d679232e08bd4d1b98766118acfba712627b/include/boost/lockfree/queue.hpp#L112-L119  
  
By the time `next_ptr == 0` check is performed, it is already guaranteed that `head == head2`, so the `next_ptr` must have set to the correct next pointer to the `head`'s next node.  
And it is guaranteed that `pool.get_handle( head ) != pool.get_handle( tail )`, so the `next_ptr` can't be `nullptr`.  
  
Even if the `head` has been reused by the time when the check performs, the `next_ptr` should hold the previous next node value instead of `nullptr`.  
  
Am I missing something here?
