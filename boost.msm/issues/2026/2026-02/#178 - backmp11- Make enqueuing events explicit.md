# #178 - backmp11: Make enqueuing events explicit [Open]

> Username: chandryan  
> Created at: 2026-02-14 10:42:03 UTC  
> Updated at: 2026-02-22 12:20:57 UTC  
> Url: https://github.com/boostorg/msm/issues/178  

**Problem statement**  
  
In `process_event` events are enqueued automatically, if the machine is already processing events.  
  
This condition has to be evaluated at runtime, which forces the compiler to instantiate an `enqueue_event` method for every event the state machine receives via `process_event`. In practice this method is usually only needed for a small subset of events, when a state machine posts an event to itself within an action. Thus most template instantiations are redundant and will never get called.  
  
**Change**  
  
Removing the automatic enqueuing eliminates the redundant template instantiations. If `process_event` is called while the state machine is processing an event, let it reject it and return `HANDLED_FALSE`. The template instance for enqueuing an event is then only instantiated if `enqueue_event` is called explicitly.  
  
**Impact**  
  
Branch with code changes to evaluate the impact: https://github.com/chandryan/msm/tree/feat/backmp11-strict-process_event-api  
  
Pros:  
  
- The API of `process_event` behaves identically with and without an event pool  
- Lower executable size: A hierarchical SM benchmark shows a reduction of ~24% (from 145kB to 110kB)  
- Faster compile time: A hierarchical SM benchmark shows a reduction of ~22% (9s to 7s)  
  
Cons:  
  
This imposes a breaking change that requires adaptations in the user code. Calls to `process_event` in actions have to be replaced with `enqueue_event`.  
  
  
The benefits are sufficient to apply this change. Replacing calls to `process_event` with `enqueue_event` is simple and straightforward.  
Also, if wanted, the automatic enqueuing can be implemented in user code by inheriting from state_machine and implementing a custom version of `process_event` (like the state machine adapter in the evaluation branch does).  
  
Especially while backmp11 is still experimental breaking changes are acceptable.  
However, this change should still come with a deprecation notice and a grace period of one release to give users time to adjust.

---

## Comment 1

> Username: chandryan  
> Created at: 2026-02-22 12:20:57 UTC  
> Url: https://github.com/boostorg/msm/issues/178#issuecomment-3940839607  

New improvement potential after the optimization witih the `flat_fold` dispatch strategy (#180), again measured with the hierarchical SM benchmark:  
  
- Lower executable size: ~39% (from 85kB to 52kB)  
- Faster compile time: ~13% (from 8s to 6.6s)
