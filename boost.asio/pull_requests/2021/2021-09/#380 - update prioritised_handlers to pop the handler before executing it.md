# #380 update prioritised_handlers to pop the handler before executing it [Open]

> Username: Hailios  
> Created at: 2021-09-21 14:55:01 UTC  
> Updated at: 2021-09-21 14:55:01 UTC  
> Url: https://github.com/boostorg/asio/pull/380  

the handler should be removed from the priority queue before it is executed to avoid removing the wrong handler in case it adds a new handler which ends up at the highest priority.

---
