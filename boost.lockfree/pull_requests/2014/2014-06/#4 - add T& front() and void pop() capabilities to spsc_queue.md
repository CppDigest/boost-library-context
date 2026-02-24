# #4 add T& front() and void pop() capabilities to spsc_queue [Merged]

> Username: erenon  
> Created at: 2014-06-25 17:21:26 UTC  
> Updated at: 2014-07-02 20:54:01 UTC  
> Merged at: 2014-07-02 20:54:01 UTC  
> Closed at: 2014-07-02 20:54:01 UTC  
> Url: https://github.com/boostorg/lockfree/pull/4  

User might want to pop elements conditionally, based on the value of the element. This PR makes possible to peek into the queue via front() and pop a single element from the front of the queue using pop().

---

## Comment 1

> Username: timblechmann  
> Created_at: 2014-06-26 04:13:48 UTC  
> Url: https://github.com/boostorg/lockfree/pull/4#issuecomment-47186051  

i'm wondering about one thing: `std::queue` provides `T& front()` and `const T & front() const`. is there any reason, not to add a non-const version?

---

## Comment 2

> Username: erenon  
> Created_at: 2014-06-26 06:51:21 UTC  
> Url: https://github.com/boostorg/lockfree/pull/4#issuecomment-47193122  

Taken care of. I don't know if there's an easier way to implement front.

---
