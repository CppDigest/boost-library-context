# #168 - backmp11: Merge queued & deferred events into a single event pool [Closed]

> Username: chandryan  
> Created at: 2026-01-30 18:24:49 UTC  
> Updated at: 2026-02-07 13:55:56 UTC  
> Closed at: 2026-02-07 13:55:56 UTC  
> Url: https://github.com/boostorg/msm/issues/168  

Investigate the possibility to merge the event containers for enqueued and deferred events into a single event pool.  
  
The event pool shall be available by default, just like the container for enqueued events currently is.  
  
Potential benefits  
---  
  
This approach takes the back-end one step closer to the UML specification and provides multiple benefits, which are further described below.  
  
### Clearer semantics for enqueuing (=deferring?) events  
  
The current functionality related to enqueueing events is somewhat similar to deferring events, but with some ambiguities in behavior.  
A call to `enqueue_event`, as documented in its API, behaves differently depending from where it is called:  
- *outside* the state machine it translates to "process the event in the next processing cycle",  
- but *inside* the statemachine it translates to "process the event in this processing cycle" and its behavior is identical to `process_event`.  
  
A unified event pool makes the `enqueue_event` API obsolete. A call to `defer_event` can be used with consistent semantics inside and outside of the state machine, with the meaning "process the event in the next processing cycle".  
  
  
### Less complexity and variance management for user code as well as library code  
  
The setting `activate_deferred_events` becomes obsolete. It is currently required to explicitly instantiate the deferred events container, if no substate defines any `deferred_events` properties but a user wants to call `defer_event`.  
Also the setting `event_queue_before_deferred_queue` becomes obsolete, as there is no need to define an order for processing event containers. Instead all events are treated equally - meaning they are processed with FIFO semantics, in the order they have been added to the event pool.  
  
### Enabler to properly handle completion transitions  
  
A PoC activity to resolve #166 actually led to the idea to provide a single event pool. The current implementation for completion transitions dispatches a completion event to activate the completion transition. The dispatch happens outside of the scope of the state that has completed its activation, *after* the previous event has been processed.  
This enables the firing of a completion event without the need to enqueue it when the state completion happens, and before the event queue and deferred events are evaluated (as mandated by the UML specification). However, the state machine cannot be certain about the existence of a completion transition for the completed state, it uses a heuristic to "guess" the completion event and tries to activate a transition. This can lead to errorneous behavior: In the best case it gracefully handles a non-existing completion transition (by not invoking the no_transition handler), in the worst case it chose the wrong completion event and thus could not reach the correct completion transition.  
  
To properly handle completion transitions under all circumstances, the completion transition needs to be directly targeted for dispatch instead of its completion event. This needs to happen in a scope where the state that has completed is known, which in turn is only known while the state machine is still processing the event that has led to the state completion.  
Consequently the processing of the completion transition has to be enqueued for later dispatch.  
  
The UML specification is very specific about the order of completion events: They need to be put to the front of the event pool and have dispatching priority. Using the existing event containers to store completion transitions does not work correctly under all circumstances, depending on the `event_queue_before_deferred_queue` setting the behavior can become errorneous. Possible options to resolve this situation are:  
  
- Implement a mechanism to put the completion events into either one container, but this increases complexity  
- Introduce a third event container only for completion events, but this still induces some level of complexity and handling 3 containers decreases runtime efficiency  
- **Use a single unified container for all 3 event types, which is the simplest and most runtime-efficient solution**  
  
This incidentally well aligns with the UML specification. It does define a single event pool that can handle different types of *EventOccurrences*, and it does even include dedicated *CompletionEventOccurrences* in this event pool 💡   
  
  
### Runtime performance benefits  
  
As soon as deferred events are used in a state machine, it needs to evaluate a second event container when processing events. Just the manual activation of the deferred events queue in the large hierarchical state machine benchmark of the [fsm-benchmarks repo](https://github.com/chandryan/fsm-benchmarks) without using event deferral leads to a noticeable performance decrease of ca. 10%.  
  
Also the following situation, although probably more of an edge case, can happen at the moment:  
- `enqueue_event`: The user enqueues an event for later processing  
- `process_event`: It leaves the event queue for processing and the state machine evaluates that the event needs to be deferred  
- `defer_event`: The event gets pushed to the deferred events queue and remains there until its final dispatch  
  
Thus the event goes through a long evaluation journey and moves 3 times: It enters the event queue, then leaves the event queue, and eventually it enters the deferred event queue. Using a single event pool with one unified deferral mechanism needs only one move into the event pool:  
- `defer_event`: The user enqueues an event for later processing, it remains there until its final dispatch  
  
  
Potential drawbacks  
---  
  
The order of deferred/enqueued event processing changes with respect to `back`. If there is a use case that relies upon behavior such as "first process all deferred events *and then* process all deferred events", it will no longer be supported.  
  
However, the UML specification leaves the order of processing unspecified and allows for "varied scheduling algorithms". IMO a consistent FIFO-style processing for all events sounds like a more consistent and intuitive scheduling algorithm, especially given the baseline that enqueued events are just a special case of "manually" deferred events.  
  
If urgent needs arise, there are still potential options available that allow for more customizable scheduling algorithms, by giving the user more freedom to choose an event from the event pool. This idea relates to another part of the UML specification (see "getNextEvent" in the PSSM doc).
