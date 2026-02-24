# #43 Fix Helgrind warning on using pthread_cond_signal while unlocked [Closed]

> Username: victorpaleologue  
> Created at: 2016-07-28 12:06:42 UTC  
> Updated at: 2017-12-02 07:39:03 UTC  
> Closed at: 2017-12-02 07:39:02 UTC  
> Url: https://github.com/boostorg/asio/pull/43  

Helgrind would report a dubious use of pthread_cond_signal before this  
patch. The original code was already correct, though, but this one seems  
to be preferred.

---

## Comment 1

> Username: victorpaleologue  
> Created_at: 2017-06-01 14:27:23 UTC  
> Url: https://github.com/boostorg/asio/pull/43#issuecomment-305510039  

@danieljames, @chriskohlhoff: how does it work to have some contribution reviewed and maybe accepted?

---

## Comment 2

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:39:02 UTC  
> Url: https://github.com/boostorg/asio/pull/43#issuecomment-348675105  

I deliberately ignore this "dubious" warning. The current implementation is better for performance, as less time is spent holding the lock.

---
