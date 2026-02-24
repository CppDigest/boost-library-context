# #108 Fixing non-reference base_t::reference case with replaced_if adaptor [Open]

> Username: m-karcz  
> Created at: 2020-08-01 18:05:16 UTC  
> Updated at: 2020-08-03 07:51:25 UTC  
> Url: https://github.com/boostorg/range/pull/108  

replaced_if is just transformed with conditional replacing functor.  
  
If base iterator returns value instead reference, conditional functor returns dangling reference to that value which ends with undefined behaviour.  
This pull request fix that by forwarding this value or returning copy of held one.  
  
Ex. on gcc-7 added test had {0,0,7,32767} as result range instead of {3,2,7,0}. After this change it returns valid range.

---

## Comment 1

> Username: m-karcz  
> Created_at: 2020-08-03 07:51:25 UTC  
> Url: https://github.com/boostorg/range/pull/108#issuecomment-667867868  

I don't know why regression fails on apple, same issue happened on PR#107.  
  
My PR doesn't touch `transformed`, just `replaced_if`, but `ticket_6742_transformed_c4789_warning` fails. Looks like some boost.phoenix breaking change.

---
