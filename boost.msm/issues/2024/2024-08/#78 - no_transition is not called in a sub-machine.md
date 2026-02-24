# #78 - no_transition is not called in a sub-machine [Closed]

> Username: not2play  
> Created at: 2024-08-24 11:57:10 UTC  
> Updated at: 2024-09-02 20:32:15 UTC  
> Closed at: 2024-09-02 11:29:27 UTC  
> Url: https://github.com/boostorg/msm/issues/78  

The behaviour of the "no_transition" functor is not documented in detail therefore perhaps this is "not a bug but a feature".  
I noticed however that the no_transition functor is not invoked when an FSM is a sub-machine of another FSM.  
I would expect the functor to be invoked whenever an event is "dropped" on the FSM and no transition occurs within that FSM - including when the FSM is the active state of a higher-level FSM.  
  
Here is a minimal reproducible example: https://godbolt.org/z/K7oa6vn8x

---

## Comment 1

> Username: henry-ch  
> Created at: 2024-09-02 11:29:27 UTC  
> Url: https://github.com/boostorg/msm/issues/78#issuecomment-2324510741  

I will add this in the doc if missing. The no_transition handler is always called only on the top-level fsm by design: if the sub fsm does not handle the event, it does not mean it cannot be handled elsewhere, for example a second orthogonal region could handle the event, it could be deferred or handled by the top within another transition.

---

## Comment 2

> Username: not2play  
> Created at: 2024-09-02 20:32:14 UTC  
> Url: https://github.com/boostorg/msm/issues/78#issuecomment-2325278475  

OK, thank you for clarifying this is by design.  
  
> if the sub fsm does not handle the event, it does not mean it cannot be handled elsewhere, for example a second orthogonal region could handle the event, it could be deferred or handled by the top within another transition.  
  
Certainly. My thinking was the fact the event might be handled elsewhere outside a sub-machine is irrelevant for that sub-machine. It would not be illogical to call `no_transition` of that sub-machine in such a case.  
  
PS It's not a feature I need - just wanted to confirm that's how it's intended to work.
