# #25 - Feature request: more builtin mp_set_* functions [Closed]

> Username: HDembinski  
> Created at: 2018-07-16 15:47:36 UTC  
> Updated at: 2019-03-05 17:07:01 UTC  
> Closed at: 2019-03-05 17:07:01 UTC  
> Url: https://github.com/boostorg/mp11/issues/25  

Mp11 is great, thank you!  
  
Some additional set operations would be nice:  
- `mp_set_union<L1, L2, ..., Ln>` computes the union of n sets  
- `mp_set_intersection<L1, L2, ..., Ln>` computes the intersection of n sets  
- `mp_set_difference<L1, L2>` computes the differences of two sets  
  
`mp_set_union` is very similar to `mp_set_push_back`, but the latter accepts Ts, not Ls. `mp_set_union` is probably simple to implement with the existing functions, but since these are common set operations, it would be nice to have these operations already defined in the library.

---

## Comment 1

> Username: kedarbhat  
> Created at: 2019-01-03 05:16:41 UTC  
> Url: https://github.com/boostorg/mp11/issues/25#issuecomment-451057621  

I wrote mp_set_union today (as a programming exercise). I’ll write intersection/difference tomorrow and submit for Peter Dimov to review (if he wants these operations in project).
