# #16 Ticket #11490: Correctly convert from allocator::pointer to T& [Merged]

> Username: pleuba  
> Created at: 2016-01-06 12:34:44 UTC  
> Updated at: 2016-01-06 13:01:06 UTC  
> Merged at: 2016-01-06 13:01:02 UTC  
> Closed at: 2016-01-06 13:01:02 UTC  
> Url: https://github.com/boostorg/lockfree/pull/16  

Do conversion from allocator::pointer to T& and then to T*, needed when spec_queue is created with special allocator, such as interprocess allocator.

---

## Comment 1

> Username: timblechmann  
> Created_at: 2016-01-06 13:01:06 UTC  
> Url: https://github.com/boostorg/lockfree/pull/16#issuecomment-169318423  

thanks a lot!

---
