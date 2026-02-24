# #162 feat(backmp11): conditional deferred_events [Merged]

> Username: chandryan  
> Created at: 2026-01-02 21:55:04 UTC  
> Updated at: 2026-02-08 13:50:48 UTC  
> Merged at: 2026-02-08 13:50:39 UTC  
> Closed at: 2026-02-08 13:50:39 UTC  
> Url: https://github.com/boostorg/msm/pull/162  

New feature for backmp11: Support conditional event deferral with the `deferred_events` property in the states.  
  
Conditional deferral can be implemented by the user via the `is_event_deferred` method, tested in `test/Backmp11Deferred.cpp`.  
  
Related to https://github.com/boostorg/msm/issues/155

---

## Comment 1

> Username: chandryan  
> Created_at: 2026-01-02 22:28:08 UTC  
> Url: https://github.com/boostorg/msm/pull/162#issuecomment-3706346871  

Would this cover your use cases for conditional event deferral @henry-ch ?

---

## Comment 2

> Username: henry-ch  
> Created_at: 2026-01-31 17:28:24 UTC  
> Url: https://github.com/boostorg/msm/pull/162#issuecomment-3828874585  

@chandryan As a welcome extension to the in-state deferral definition, yes.  
As a complete replacement, no. The tranisition table definition is still allowing more powerful constructs:  
Row<State1, event, none,   Defer, Not_<can_handle_now>>,  
Row<State1, event, State2, none , can_handle_now>   
  
Which means "if you can handle the event and transition now, do it, otherwise defer until it can be done".  
Of course I am slightly biased because I like to move everything into a transition table, but nevertheless I think this use case it valid.  
I am unsure if you mean as an addition or replacement. As an addition, then cool, I like it. As a replacement, not so good.

---

## Comment 3

> Username: chandryan  
> Created_at: 2026-02-02 21:13:36 UTC  
> Url: https://github.com/boostorg/msm/pull/162#issuecomment-3837458522  

> @chandryan As a welcome extension to the in-state deferral definition, yes. As a complete replacement, no. The tranisition table definition is still allowing more powerful constructs: Row<State1, event, none, Defer, Not_<can_handle_now>>, Row<State1, event, State2, none , can_handle_now>  
>   
> Which means "if you can handle the event and transition now, do it, otherwise defer until it can be done". Of course I am slightly biased because I like to move everything into a transition table, but nevertheless I think this use case it valid. I am unsure if you mean as an addition or replacement. As an addition, then cool, I like it. As a replacement, not so good.  
  
Thanks for the feedback and the example!  
It's going to be an extension and `defer_event` will stay public 👍. I will update the documentation accordingly before merging this.

---

## Comment 4

> Username: henry-ch  
> Created_at: 2026-02-05 07:33:24 UTC  
> Url: https://github.com/boostorg/msm/pull/162#issuecomment-3851549275  

Then I like it :)

---
