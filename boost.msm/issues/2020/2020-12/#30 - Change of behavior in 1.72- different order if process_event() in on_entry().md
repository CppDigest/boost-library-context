# #30 - Change of behavior in 1.72: different order if process_event() in on_entry() [Closed]

> Username: BrukerJWD  
> Created at: 2020-12-08 16:15:19 UTC  
> Updated at: 2024-02-08 16:02:33 UTC  
> Closed at: 2024-02-08 16:02:33 UTC  
> Url: https://github.com/boostorg/msm/issues/30  

I wrote a state machine with Boost 1.64. As it seemed most convenient for me, I put all actions into the `on_entry()` functions. For error handling I created a `ErrorState` that all states have a transition to if an `ErrorEvent` is signalled. This event is mostly sent internally, e.g. if a calculation over the measured data produces invalid results.  
  
Now I changed to Boost 1.72 and my unit tests start to fail, because the ErrorEvent is now queued and only processed after the transition table. I do have some immediate transactions to separate the logic which now happen although the ErrorEvent is sent.   
  
I wrote a small reproducer on [wandbox](https://wandbox.org/permlink/2SDVu8FQCAh3WFia). It has 3 states (State1, 2 and 3) + the ErrorState. According to the transition table, `State1` transitions to `State2` on a `StartEvent`, `State2` immediately to `State3` and `State3` immediatly back to `State1`. `State2` also has an entry for an `ErrorEvent` to transition to `ErrorState", and in `on_entry()` of `State2` the `ErrorEvent` is emitted. Until Boost 1.71 this put the state machine into the `ErrorState`, but since 1.72 the transition table is processed first.  
  
If this program is executed with 1.71, the output is:  
  
    State1::on_entry after N5boost3msm4back13state_machineI2SmNS_9parameter5void_ES5_S5_S5_E9InitEventE  
    State2::on_entry after 10StartEvent  
    ErrorState::on_entry after 10ErrorEvent  
  
whereas with 1.72 it is:  
  
    State1::on_entry after N5boost3msm4back13state_machineI2SmNS_9parameter5void_ES5_S5_S5_E9InitEventE  
    State2::on_entry after 10StartEvent  
    State3::on_entry after N5boost3msm5front4noneE  
    State1::on_entry after N5boost3msm5front4noneE  
    no transition from state State1 (0) on event 10ErrorEvent  
  
I am not sure whether this is intended behavior.

---

## Comment 1

> Username: henry-ch  
> Created at: 2024-02-08 16:02:33 UTC  
> Url: https://github.com/boostorg/msm/issues/30#issuecomment-1934440386  

Sorry for the very long dealy :(  
The behavior in 1.72 is correct. According to the UML Standard, completion events (none) have the highest priority, so that the event queue can only be processed after yóur FSM transitions back to State1.
