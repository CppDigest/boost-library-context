# #41 - Dispatching priority of events [Closed]

> Username: chaoflow  
> Created at: 2021-07-05 00:11:03 UTC  
> Updated at: 2024-02-07 17:37:21 UTC  
> Closed at: 2024-02-07 17:37:20 UTC  
> Url: https://github.com/boostorg/msm/issues/41  

From UML 2.5.1 I take the following dispatching priority:  
  
1. completion event  
2. deferred event if:  
    - the deferring state is still active, but a guard changed so one of its transitions can use it  
    - OR no active state is deferring it, so it is either used or discarded  
3. an event from the event queue  
  
Reading [msm's description](https://www.boost.org/doc/libs/1_74_0/libs/msm/doc/HTML/ch06.html#d0e3013) of its run-to-completion algorithm, it seems to me that the priorities are:  
  
1. event passed to process_event  
2. first deferred event (are multiple deferred events supported?)  
3. all queued events  
4. completion events  
  
Am I understanding things correctly and if yes, what is the motivation for the different priorities?

---

## Comment 1

> Username: henry-ch  
> Created at: 2024-02-07 17:37:20 UTC  
> Url: https://github.com/boostorg/msm/issues/41#issuecomment-1932552745  

The documentation is wrong, completion events are at position 2 (MSM requires a transition to be processed before it can check for completion events as it uses no threads)
