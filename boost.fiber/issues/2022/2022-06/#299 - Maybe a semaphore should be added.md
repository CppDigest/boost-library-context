# #299 - Maybe a semaphore should be added [Open]

> Username: inie0722  
> Created at: 2022-06-07 02:56:08 UTC  
> Updated at: 2024-05-02 10:20:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/299  

buffered_channel requests heap memory. In some simple scenarios this can be very inefficient. If a semaphore is provided, it can make it easier for its users to implement their own message queues.

---

## Comment 1

> Username: Kered13  
> Created at: 2024-05-02 10:20:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/299#issuecomment-2090104978  

+1 I was just looking for a semaphore and was surprised but to see one already. It would be a very useful addition.
