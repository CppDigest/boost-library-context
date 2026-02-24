# #1 add T& front() and void pop() capabilities to spsc_queue [Closed]

> Username: erenon  
> Created at: 2014-06-22 21:52:09 UTC  
> Updated at: 2014-06-23 19:47:21 UTC  
> Closed at: 2014-06-23 17:51:10 UTC  
> Url: https://github.com/boostorg/lockfree/pull/1  

User might want to pop elements conditionally, based on the value of the element. This PR makes possible to peek into the queue via front() and pop a single element from the front of the queue using pop().

---

## Comment 1

> Username: erenon  
> Created_at: 2014-06-23 17:51:10 UTC  
> Url: https://github.com/boostorg/lockfree/pull/1#issuecomment-46878482  

Defect: there is no way to use front() in a reliable way from a consumer thread.

---

## Comment 2

> Username: timblechmann  
> Created_at: 2014-06-23 19:25:48 UTC  
> Url: https://github.com/boostorg/lockfree/pull/1#discussion_r14094685  

the implementation of pop() could be simplified by implementing it via consume_one() with an empty functor. this simplifies the code a bit

---

## Comment 3

> Username: timblechmann  
> Created_at: 2014-06-23 19:27:39 UTC  
> Url: https://github.com/boostorg/lockfree/pull/1#discussion_r14094782  

add a note and an assertion that `read_available() > 0`

---

## Comment 4

> Username: erenon  
> Created_at: 2014-06-23 19:44:02 UTC  
> Url: https://github.com/boostorg/lockfree/pull/1#discussion_r14095712  

struct consume_via_copy is in detail/copy_payload.hpp. Shall I put consume_noop next to it or create a separate file?

---

## Comment 5

> Username: timblechmann  
> Created_at: 2014-06-23 19:47:21 UTC  
> Url: https://github.com/boostorg/lockfree/pull/1#discussion_r14095891  

yes that file is probably the best place for it!

---
