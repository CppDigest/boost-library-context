# #166 - Completion events fire too often [Closed]

> Username: chandryan  
> Created at: 2026-01-28 21:56:27 UTC  
> Updated at: 2026-02-09 19:11:32 UTC  
> Closed at: 2026-02-09 19:11:32 UTC  
> Url: https://github.com/boostorg/msm/issues/166  

When completion events / completion transitions are used in a MSM state machine, they fire too often.  
Consider the following example (full code [here](https://github.com/chandryan/msm/tree/fix/backmp11-completion-transitions)):  
  
```  
// State machine  
using initial_state = State1;  
using transition_table = mp11::mp_list<  
    //    Start   Event   Next  Action  Guard  
    Row < State1, none  , none, none  , ConditionIsTrue >,  
    Row < State1, Ignore, none                          >  
>;  
...  
// Test case  
state_machine.start();  
BOOST_REQUIRE(state_machine.guard_calls == 1);  
  
// Firing Ignore leads to firing an unexpected completion event.  
state_machine.process_event(Ignore());  
// BOOST_REQUIRE(state_machine.guard_calls == 1);  
BOOST_REQUIRE(state_machine.guard_calls == 2);  
  
// Enabling the condition and firing Ignore leads to infinite recursion.  
state_machine.condition = true;  
state_machine.process_event(Ignore());  
```  
  
The specification for completion transitions and the implementation in MSM seem to deviate in the following ways:  
  
*Specification:*  
--  
A completion event is fired once a state is entered, its entry action has completed and its `doActicity` behavior has finished. These `doActivity` behaviors are potentially long-running tasks within a state and do not exist in MSM, thus a completion event should fire after the completion of the state's entry action.  
  
The PSSM document p. 53 mentions one additional detail: "The scope of a CompletionEventOccurrence is limited to the StateActivation from which it was generated [...]." This effectively means that only the completion transition belonging to the state is allowed to be activated.  
  
*Implementation:*  
--  
A completion event in MSM is fired as soon 2 conditions are true:  
- an event has been fired and handled *and*  
- there exists at least one completion transition in the transition table of the state machine  
  
When these conditions are true, the first found completion event in the transition table will be dispatched to the state machine.  
  
The example code showcases 2 observed unexpected behaviors:  
- Everytime an event is handled, a completion event is fired and evaluated by the state machine  
- In case the completion transition activates but does not change the target state, this leads to infinite recursion: The completion event is considered handled by the completion transition, which leads to another completion event firing  
  
There are other situations that can lead to unexpected behavior:  
- activations of unrelated completion transitions, if orthogonal regions are involved  
- dispatching of "wrong" completion events, in case custom completion events are defined and they are not the first found completion event in the transition table  
  
---  
  
Further analysis is needed to evaluate whether completion events can be handled as specified.

---

## Comment 1

> Username: chandryan  
> Created at: 2026-02-08 16:18:28 UTC  
> Updated at: 2026-02-08 16:21:58 UTC  
> Url: https://github.com/boostorg/msm/issues/166#issuecomment-3867486655  

With the event pool and its generic `event_occurrence` interface (#168) it's now possible to implement a *completion event occurrence* and push it the front of the event pool as specified in UML.  
The implementation can directly target the matching completion transition from the transition table and fire it only after state entry.  
  
For the time being, completion transitions of submachines need to be excluded from the implementation.  
These shall fire when all regions of the submachine reach their final state, but final states do currently not exist in MSM..
