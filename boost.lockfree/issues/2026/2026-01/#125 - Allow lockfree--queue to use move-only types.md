# #125 - Allow lockfree::queue to use move-only types [Open]

> Username: Spacechild1  
> Created at: 2026-01-10 03:58:56 UTC  
> Updated at: 2026-01-10 04:01:31 UTC  
> Url: https://github.com/boostorg/lockfree/issues/125  

I think `lockfree::queue` could actually support move-only types as long as there are no concurrent pop operations. (If there is only a single consumer, nobody can concurrently "steal" the data.)  
  
I guess this could be implemented as a compile time policy.
