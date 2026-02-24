# #155 - backmp11: Support conditional deferral with the `deferred_events` property [Closed]

> Username: chandryan  
> Created at: 2025-11-22 12:01:17 UTC  
> Updated at: 2026-02-08 14:29:29 UTC  
> Closed at: 2026-02-08 14:29:29 UTC  
> Url: https://github.com/boostorg/msm/issues/155  

### Problem statement  
  
MSM currently allows 2 ways to defer events:  
  
- Via a `deferred_events` property in a state and  
- Via an action within a transition  
  
The first possibility is consistent with the UML standard, but the second one is not foreseen by it. While this mechanism supports more than what is foreseen by UML, such as conditional event deferrals with the help of guards in the transition, it leads to undesired (and likely unsolvable, see https://github.com/boostorg/msm/issues/72 for more details) issues:  
  
- due to the need to dispatch the event for evaluation, UML-compliant behavior cannot be ensured with this mechanism in orthogonal regions; events can be deferred and processed by different regions at the same time   
- the event has to be removed from the deferred events queue and dispatched for evaluation and then get pushed back to it, leading to potential issues wrt. deferred event orders in the queue - worst case to infinite recursion  
  
  
### Possible solutions  
  
For `backmp11` there are 2 possiblities to handle the problem:  
  
1. Remove support for event deferral as an action, as it's anyway not foreseen by UML  
2. Provide an alternative approach that allows deferral evaluation at runtime, but is more similar to the `deferred_events` state property  
  
  
### Feedback required ❗   
  
Solution no. 1 "remove support" is currently targeted for the next Boost release 1.91, mainly because it's not clear whether this functionality is needed in the first place.  
  
If a more sophisticated event deferral mechanism than described in the UML standard is needed, please provide feedback by stating your use case and your needs.

---

## Comment 1

> Username: henry-ch  
> Created at: 2025-11-29 17:42:34 UTC  
> Url: https://github.com/boostorg/msm/issues/155#issuecomment-3591829012  

Strong vote against Solution 1. If you think of a solution 2, I am interested :)

---

## Comment 2

> Username: chandryan  
> Created at: 2025-12-05 04:52:01 UTC  
> Updated at: 2025-12-05 04:52:09 UTC  
> Url: https://github.com/boostorg/msm/issues/155#issuecomment-3615302845  

Thanks for the feedback! I'm fairly confident we'll find a good solution 2 which works well with the "UML" way of deferring events.  
  
For solution 1, "removal" was the technically imprecise short statement 😅   
What I would like to do in backmp11 in detail is the following:  
Change the access modifier of `state_machine`'s `defer_event` method from public to protected. Deferring as an action is not actually removed then, but you need to opt-in by inheriting from `state_machine` and making the method public again. This would express "I'm aware there are limitations, I want to do it anyway and I know that I can shoot myself in the foot".  
  
  
For solution 2 I have a member function in the state like this in mind:  
  
```  
template <class Event, class Fsm>  
bool is_event_deferred(const Event&, Fsm&) const;  
```  
  
The function would be evaluated complementary to the `deferred_events` property, in pseudo-code: `is_in_deferred_events<State, Event>::value || state.is_event_deferred(event, fsm)`  
  
This should provide similar possibilities to deferring the event in an action, but the evaluation can happen prior to event dispatch.  
Please let me know if you have any doubts about the idea, I'll add you the Pull Request when I have set something up that works :)

---

## Comment 3

> Username: chandryan  
> Created at: 2025-12-19 14:50:24 UTC  
> Updated at: 2026-01-02 22:34:53 UTC  
> Url: https://github.com/boostorg/msm/issues/155#issuecomment-3675353633  

I've spent some time on this topic and was able to [draft an implementation](https://github.com/chandryan/msm/tree/feat/backmp11-conditional-deferral) with a `conditionally_deferred_events` property. It does work technically, though there might still be better solutions.  
  
I see 3 possible approaches so far, sharing a summary of my thoughts below.  
~Out of these, approach C "new row types for deferral" seems to be the most promising for me currently.~  
  
---  
  
**A: Additional property in the state**  
  
Approach:  
- re-use the existing `deferred_events` property in the state for conditionally and unconditionally deferred events  
- add a `bool is_event_deferred(...)` method with the same arguments as `on_entry` and `on_exit`, which by default returns true for all events  
  
Pros:  
- Most similar to what UML imagines how deferral should look like (-> having a state property)  
- Withholds separation of concerns regarding transition vs. deferral  
- Fits well with the already existing API  
  
Cons:  
- maybe the fact that deferrals cannot be prioritized with transitions in the transition table, though maybe that's better for the sake of understandability with respect to UML resemblance and complexity of the SM's behavior in general?   
  
---  
  
**B: Improve deferral as action**  
  
Approach:  
- Split transition execution into check (for preprocessing) and execute  
- First preprocess all orthogonal regions and then conditionally execute or defer  
  
Pros:  
- Can be priorized with other rows in the transition table  
-> Supports edge cases, for example "override" a deferral  
  
Cons:  
- Deferral as action is semantically contradictory, at that point the event should already be considered *consumed* and deferral should not be possible anymore  
- Having a target state in these rows is confusing, it's not clear to me if that transition should happen or not  
- Runtime overhead of such a split, though being only a single function call, is non-negligible (made it to [some working code in favor_compile_time](https://github.com/chandryan/msm/tree/feat/backmp11-transition-preprocessing) and even there the runtime of the benchmark increased quite a bit). I was not aware of how well optimized the generated code is 😅   
- The runtime overhead could only be mitigated with compile-time filtering to evaluate which states need preprocessing, which would still increase compile time in a non-neglibible manner because the whole transition table needs to be filtered. And also make the code base vastly more complicated (all processing paths need to support "preprocess + execute" but also "execute directly")  
  
---  
  
**C: New row types for deferral**  
  
Approach:  
- Similar to B, but with dedicated row types instead of deferral as action  
- New `DeferRow`: With source state, event and (optional) guard  
- New `DeferInternal`: With event and (optional) guard  
  
Pros:  
- Same as B, but clearer semantics than deferral as action  
  
Cons:  
- Same as B  
  
This could have been "the one" universal mechanism to do conditional and unconditional deferral and has the potential to supersede the `deferred_events` property. If it was possible to be done without these major drawbacks on the code base or compile time.

---

## Comment 4

> Username: chandryan  
> Created at: 2026-01-02 22:41:56 UTC  
> Url: https://github.com/boostorg/msm/issues/155#issuecomment-3706393939  

I've made progress on the implementation approaches and did not find a suitable solution for B or C, updated the previous message above with my insights.  
  
However, for approach A I was able to optimize the code so that the compile time and runtime now practically only increase if deferred events are really used, and in that case only for execution paths for events that can actually be deferred. I could also simplify the definition of conditional deferral by re-using the existing `deferred_events` property, the usage now looks simple and straightforward to me.  
  
The MR #162 is ready with the implementation and extended test cases for conditional deferral :)
