# #107 - Small copy-paste-error in documentation of mp_push_back [Closed]

> Username: Ukilele  
> Created at: 2025-09-09 09:34:23 UTC  
> Updated at: 2025-09-09 13:37:37 UTC  
> Closed at: 2025-09-09 13:37:37 UTC  
> Url: https://github.com/boostorg/mp11/issues/107  

Looking at https://www.boost.org/doc/libs/latest/libs/mp11/doc/html/mp11.html#mp_push_backl_t, it says  
  
> Code Example 31. Using mp_push_back with mp_list_v  
using L3 = mp_list_v<0, 1>;  
using R3 = mp_push_front<L3, mp_true, mp_false>; // mp_list_v<0, 1, true, false>  
  
  
I think this is a small typo. Instead of `mp_push_front` it should say `mp_push_back`.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-09-09 13:33:17 UTC  
> Url: https://github.com/boostorg/mp11/issues/107#issuecomment-3270774792  

It most certainly should. :-)
