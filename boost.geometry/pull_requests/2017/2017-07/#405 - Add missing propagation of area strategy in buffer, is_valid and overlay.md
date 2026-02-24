# #405 Add missing propagation of area strategy in buffer, is_valid and overlay. [Merged]

> Username: awulkiew  
> Created at: 2017-07-04 02:53:21 UTC  
> Updated at: 2018-01-23 00:12:09 UTC  
> Merged at: 2017-07-05 11:51:43 UTC  
> Closed at: 2017-07-05 11:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/405  

The area strategy isn't propagated in all places where it should be, default strategy is used. This PR changes that.  
  
It also adds strategy parameter to `correct()` algorithm allowing to pass area strategy.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2017-07-04 06:21:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/405#issuecomment-312790542  

Good! Do you want to get this on 1.64?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2017-07-04 13:58:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/405#issuecomment-312885133  

Yes, I'd like to release it with 1.64.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2017-07-05 02:37:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/405#issuecomment-312982686  

And by 1.64 I (and probably you too) mean 1.65 ;)

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2017-07-05 13:01:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/405#issuecomment-313096137  

Sure, of course, 1.65 :-)

---
