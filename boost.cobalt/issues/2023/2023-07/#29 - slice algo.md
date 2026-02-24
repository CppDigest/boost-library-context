# #29 - slice algo [Closed]

> Username: klemens-morgenstern  
> Created at: 2023-07-22 16:47:51 UTC  
> Updated at: 2024-05-02 00:58:56 UTC  
> Closed at: 2024-05-02 00:58:56 UTC  
> Url: https://github.com/boostorg/cobalt/issues/29  

A possible algo akin to select could be `slice` (insert better name here) that does a random (or left-to-right for `left_slice`) check of awaitables, and stops once it finds one that's ready / completes immediately.   
  
it would return a tuple/range of optionals or a bitset/vector<bool> if all are void.  
  
This would not require interruption.  
  
A variant on this algorithm could be an algo that doesn't wait but just returns those ready/completed immediatly and interrupts/completes everything else.  
  
@madmongo1

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-04 03:20:40 UTC  
> Url: https://github.com/boostorg/cobalt/issues/29#issuecomment-1664915847  

Probably should be named `partial_join` or `join_ready` - and `partial_gather` / `gather_ready

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2024-05-02 00:58:56 UTC  
> Url: https://github.com/boostorg/cobalt/issues/29#issuecomment-2089358215  

I'll consider that over-engineered until I have an actual use-case.
