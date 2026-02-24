# #8 Move semantics; no DefaultConstructible<T>; bugs [Closed]

> Username: rdaysky  
> Created at: 2015-01-09 19:02:12 UTC  
> Updated at: 2015-08-09 17:16:52 UTC  
> Closed at: 2015-08-09 17:16:52 UTC  
> Url: https://github.com/boostorg/lockfree/pull/8  

The spsc_queue suffers from several design and implementation flaws.  
This commit achieves the following:  
- Implements C++11 move semantics for push.  
- Drops the unnecessary requirement that T be default constructible.  
- Fixes reset() not working at all.  
- Fixes compile-time-sized buffer not calling element destructors.  
- Converts regular inheritance to CRTP to lessen the need for proxy  
  functions.  
  
Remains to be done:  
- Inheritance is still unnecessarily convoluted.  
- There are functions that have no place inside the queue class itself  
  that should be deprecated in favor of externally constructed  
  iterators.  
- Implementation can be streamlined, though with care, concurrency is  
  hard.

---

## Comment 1

> Username: timblechmann  
> Created_at: 2015-01-23 05:12:30 UTC  
> Url: https://github.com/boostorg/lockfree/pull/8#issuecomment-71148717  

- could you please split this into smaller PRs, each addressing one issue? it is really hard to review this.  
- pull requests should target the develop branch, not master.  
  
thnx!

---

## Comment 2

> Username: rdaysky  
> Created_at: 2015-01-26 16:56:27 UTC  
> Url: https://github.com/boostorg/lockfree/pull/8#issuecomment-71494214  

Almost all of the patch is fixing the inheritance which was very roundabout, for that reason the changes are all over the code and it’s hard to separate minor fixes from the inheritance-related differences.  
  
Move-sematics-related changes can be easily found by looking for BOOST_LOCKFREE_MOVE_\* macros.

---

## Comment 3

> Username: timblechmann  
> Created_at: 2015-08-09 17:16:52 UTC  
> Url: https://github.com/boostorg/lockfree/pull/8#issuecomment-129217970  

closing. please reopen after resolving the conflicts

---
