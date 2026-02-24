# #2 add T& front() and void pop() capabilities to spsc_queue [Closed]

> Username: erenon  
> Created at: 2014-06-23 19:25:41 UTC  
> Updated at: 2014-06-23 20:05:59 UTC  
> Closed at: 2014-06-23 20:05:59 UTC  
> Url: https://github.com/boostorg/lockfree/pull/2  

User might want to pop elements conditionally, based on the value of the element. This PR makes possible to peek into the queue via front() and pop a single element from the front of the queue using pop().

---

## Comment 1

> Username: timblechmann  
> Created_at: 2014-06-23 19:31:47 UTC  
> Url: https://github.com/boostorg/lockfree/pull/2#issuecomment-46890635  

i've added some comments to #1. if you can update this pr with the comments of the other one, i'll merge it!

---

## Comment 2

> Username: erenon  
> Created_at: 2014-06-23 19:56:00 UTC  
> Url: https://github.com/boostorg/lockfree/pull/2#issuecomment-46893310  

Please revisit.

---

## Comment 3

> Username: timblechmann  
> Created_at: 2014-06-23 20:02:53 UTC  
> Url: https://github.com/boostorg/lockfree/pull/2#issuecomment-46894305  

just one more thing: can you retarget the PR to the develop branch instead of master? things first have to cycle the tests before they can be merged in to master

---
