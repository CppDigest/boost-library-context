# #173 - backmp11: Improve support for the `deferred_events` property in hierarchical state machines [Closed]

> Username: chandryan  
> Created at: 2026-02-07 15:53:53 UTC  
> Updated at: 2026-02-21 19:22:47 UTC  
> Closed at: 2026-02-21 19:22:47 UTC  
> Url: https://github.com/boostorg/msm/issues/173  

In backmp11 the `deferred_events` property of the substates is evaluated with a visitor. The current implementation traverses active states non-recursively to evaluate whether an event should be deferred.  
  
We can extend the implementation to perform this evaluation recursively, which results in better support for hierarchical state machines:  
  
**Faster:**  
The complete active state configuration is considered in one go, enabling deferral without intermediate event dispatches due to `process_event` calls to submachines.  
  
  
**Less error prone:**  
Without a recursive visitor, an event can be forwarded for processing and then put into the event pool of a submachine.  
Events from the event pool of the submachine can only be consumed further down the hierarchy, other submachines on the same hierarchy level cannot receive it anymore.  
  
With a recursive visitor, the event will be stored in the event pool of the machine that was requested to process the event. This would usually be the root state machine. And in this case all submachines can receive the event later.  
  
  
**Enabler for potential removal of event pools in submachines:**  
If the needs for an event pool in hierarchical state machines can be covered by the event pool residing in the root state machine, then the submachines' event pools are eligible for removal. This is an interesting prospect for resource-constrained systems to reduce memory footprint of the state machine, though it's probably negligible in other cases.
