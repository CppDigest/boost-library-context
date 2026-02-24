# #28 changed: mp_unique, new: mp_xor, mp_set_intersection, mp_set_difference, mp_set_symmetric_difference, mp_set_union [Closed]

> Username: kedarbhat  
> Created at: 2019-01-07 06:25:04 UTC  
> Updated at: 2019-01-08 02:14:01 UTC  
> Closed at: 2019-01-08 02:14:01 UTC  
> Url: https://github.com/boostorg/mp11/pull/28  

function.hpp: Added mp_xor  
algorithm.hpp: Changed mp_unique to remove dependency on set.hpp (so that algorithm.hpp can be used with sets)  
set.hpp: Added mp_set_intersection, mp_set_difference, mp_set_symmetric_difference, mp_set_union (in response to [https://github.com/boostorg/mp11/issues/25](url))

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-01-07 15:25:43 UTC  
> Url: https://github.com/boostorg/mp11/pull/28#issuecomment-451970861  

You're not supposed to change the semantics of `mp_set_push_back`, sorry. The correct result of `mp_set_push_back<mp_list<X, Y>, mp_list<Z>>` is `mp_list<X, Y, mp_list<Z>>` and not `mp_list<X, Y, Z>`.  
  
(As it happens, I implemented `mp_set_union` yesterday.)

---

## Comment 2

> Username: kedarbhat  
> Created_at: 2019-01-07 15:33:23 UTC  
> Updated_at: 2019-01-07 15:37:01 UTC  
> Url: https://github.com/boostorg/mp11/pull/28#issuecomment-451973637  

I apologize. Would you like me to correct the set operations (accounting  
for unchanged mp_set_push_back semantics) and resubmit (without  
mp_set_union), or are you planning to implement the other set operations  
(or leave them out)?  
  
On Mon, Jan 7, 2019 at 9:25 AM Peter Dimov <notifications@github.com> wrote:  
  
> You're not supposed to change the semantics of mp_set_push_back, sorry.  
> The correct result of mp_set_push_back<mp_list<X, Y>, mp_list<Z>> is mp_list<X,  
> Y, mp_list<Z>> and not mp_list<X, Y, Z>.  
>  
> (As it happens, I implemented mp_set_union yesterday.)  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/mp11/pull/28#issuecomment-451970861>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AHlYFuVqkdo9_k9MMrSMhCW0q3zIzUsjks5vA2b4gaJpZM4Zy7mS>  
> .  
>

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-01-07 16:54:22 UTC  
> Url: https://github.com/boostorg/mp11/pull/28#issuecomment-452002259  

The remaining set operations are on my to-do list, along with some other additions.

---

## Comment 4

> Username: kedarbhat  
> Created_at: 2019-01-08 02:14:01 UTC  
> Url: https://github.com/boostorg/mp11/pull/28#issuecomment-452150327  

I updated the operations in the style you wrote mp_set_union, but I see you've created branches for most of the operations (and implementations/tests). I'll close this PR.

---
