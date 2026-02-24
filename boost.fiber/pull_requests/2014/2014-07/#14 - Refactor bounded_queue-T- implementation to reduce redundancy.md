# #14 Refactor bounded_queue<T> implementation to reduce redundancy. [Merged]

> Username: nat-goodspeed  
> Created at: 2014-07-16 16:12:10 UTC  
> Updated at: 2016-06-06 11:36:33 UTC  
> Merged at: 2014-07-18 12:46:01 UTC  
> Closed at: 2014-07-18 12:46:01 UTC  
> Url: https://github.com/boostorg/fiber/pull/14  

Introduce private push_(), try_push_(), push_wait_until_() helper methods:  
each of push(), try_push() and push_wait_until() has two signatures for  
value_type const& versus value_type&&, but the code paths are identical once  
we have a new node_type in hand. Moreover, extract processing common to all  
into private push_and_notify_() method. (This fixes an inconsistency in push()  
exception behavior.)  
  
Extract common processing from pop(), value_pop(), try_pop() and  
pop_wait_until() to private value_pop_() method. This retains a node_type::ptr  
to the old head node until return time, unifying the code paths between its  
return-by-value and assign-through-reference callers. (This fixes an  
inconsistency in try_pop() exception behavior.)

---
