# #122 Fix sync_bounded_queue<>::size() [Merged]

> Username: vincenthsu  
> Created at: 2017-05-10 05:44:09 UTC  
> Updated at: 2017-05-10 23:33:44 UTC  
> Merged at: 2017-05-10 23:33:44 UTC  
> Closed at: 2017-05-10 23:33:44 UTC  
> Url: https://github.com/boostorg/thread/pull/122  

I create a sync_bounded_queue with capacity 10 and then push 1 item into the queue. The size() functions will get 9, but it should be 1.

---

## Comment 1

> Username: viboes  
> Created_at: 2017-05-10 23:33:29 UTC  
> Url: https://github.com/boostorg/thread/pull/122#issuecomment-300641302  

Thanks a lot for this fix. I really need to add more test :)

---
