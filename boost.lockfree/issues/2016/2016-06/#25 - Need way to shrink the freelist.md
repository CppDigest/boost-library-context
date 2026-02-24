# #25 - Need way to shrink the freelist [Open]

> Username: stephenmax-zhang  
> Created at: 2016-06-10 08:35:55 UTC  
> Updated at: 2016-06-12 01:36:55 UTC  
> Url: https://github.com/boostorg/lockfree/issues/25  

Hi,everybody~  
  
I found nodes in freelist can never be shrinked, it can only grow larger(like "reserve_unsafe" and "reserve" do).  
The only way to reduce the capacity of freelist is destruct queue,ringbuffer or stack. But that is not convenient. Sorry I must make the memory occupy not so high affter some request burst.  
Is it possible to improve that?  
If cann't, then "capacity_unsafe" function to tell current capacity is needed.  
I understand data structure in freelist implementation should lock free as much as possible. But it can have a flexible way like limit the max freelist capacity. Such as when return node to freelist, examine the current capacity. It doesn't need to be accurate,so "capacity" doesn't need to be a atmoic variable.  
Just some my silly ideas~  
  
Thanks~

---

## Comment 1

> Username: timblechmann  
> Created at: 2016-06-11 09:21:26 UTC  
> Url: https://github.com/boostorg/lockfree/issues/25#issuecomment-225346779  

not really:  
- freeing memory is not lock-free  
- freeing memory needs some safe memory reclamation algorithm like pass the buck or hazard pointers. both are patented.  
  
one could add a method to have an 'unsafe' memory reclamation, which has undefined behavior when multiple threads access the queue, but i'm not a big fan of adding unsafe API as people won't be aware of the consequences.

---

## Comment 2

> Username: stephenmax-zhang  
> Created at: 2016-06-12 01:36:55 UTC  
> Url: https://github.com/boostorg/lockfree/issues/25#issuecomment-225404292  

Thank you for your reply.  
The "request burst" I talking about is not too often, but due to the mass amount lockfree.queue instances I used, I must take care of this.  
I have appropriate timing window to shrink the queue (i.e. when I certain it will not accessed by other thread), and it only need to be shrinked after every long period per session, so "freeing memory is not lock-free" is not my problem.   
Adding the shrinking API is too difficult for me, so I appreciate if it comes from the author.  
Sorry for my poor english~
