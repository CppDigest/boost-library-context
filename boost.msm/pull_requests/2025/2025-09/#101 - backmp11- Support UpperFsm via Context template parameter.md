# #101 backmp11: Support UpperFsm via Context template parameter [Closed]

> Username: chandryan  
> Created at: 2025-09-14 10:51:22 UTC  
> Updated at: 2025-11-10 20:53:23 UTC  
> Closed at: 2025-10-09 21:13:21 UTC  
> Url: https://github.com/boostorg/msm/pull/101  

Hi @henry-ch 😀   
  
I looked into how backmp11 can support access to the `UpperFsm` of hierarchical SMs (similar to how you did it in back11) and propose to add a `Context` template parameter. It's very similar to the `UpperFsm` template parameter of back11, but has some benefits which I'll explain further below.  
  
I tried out 3 different approaches for which I have demo MRs in my fork:  
  
### **[Replace the Fsm parameter with the UpperFsm at runtime:](https://github.com/chandryan/msm-ng/pull/9)**  
  
Requires lots of code changes. I managed to get an exemplary test case running, but more changes would be required to correctly forward the UpperFsm in all methods.  
  
While from a user perspective this is nice if only access to the UpperFsm is needed, the need to forward the UpperFsm in all related methods has a very big and rather negative impact on the code base. And it seems to completely remove the possibility to access the currently active sub-FSM if needed :(  
  
Considering that a mechanism to *optionally* select the UpperFsm has less impact on the code base and is more universal, I decided to give up on this implementation.  
  
  
### **[Use a `UpperFsm` template parameter like in back11:](https://github.com/chandryan/msm-ng/pull/8)**  
  
I stumbled upon some issues which already were present in `back11`, that would need to be resolved:  
  
IMO for the sake of simplicity in implementations of actions, the upper fsm should return a pointer to itself when its `get_upper()` API is called. I couldn't find a nice way to "mark" the upper SM without a negative impact on the maintainability of the implementation in MSM:  
- Either you mark the upper fsm's template parameter with `void`, which requires additional code to handle the upper fsm itself  
- Or you pass the UpperFsm as template parameter, which creates issues due to assigning to a child class when the backend implementation uses inheritance instead of a using directive. Additionally you'd need additional code to implement some kind of post-construction mechanism to make the upper sm store a pointer to itself and then pass itself to its sub-SMs  
  
There is also one location where I needed a reinterpret_cast in the demo MR, of which I'm not sure of the root cause.  
  
  
### **[Use a `Context` template parameter:](https://github.com/chandryan/msm-ng/pull/7)**  
  
This implementation is very similar to the `UpperFsm` template parameter, though it's more generic:  
  
- It doesn't impose assumptions on what it contains: It *could* be the `UpperFsm`, or it could be some data shared between all FSMs, or it could be both  
- Users can pass different instances of this Context at construction time. This makes it a perfect candidate for dependency injection  
  
The API for accessing the context is very similar to the one for accessing the upper fsm in `back11`: `UpperFsm* get_upper()` becomes `Context* get_context()`.  
  
From implementation perspective, it doesn't require custom code for the instantiation of the UpperFsm itself and the semantics are less ambiguous: With `get_upper()` it might not be clear whether the UpperFsm should return a nullptr or a pointer to itself. With a Context mechanism it's clear that all SMs, including the UpperFsm, return a pointer to the context.

---

## Comment 1

> Username: chandryan  
> Created_at: 2025-09-14 12:59:27 UTC  
> Url: https://github.com/boostorg/msm/pull/101#issuecomment-3289525943  

Update:  
  
As existing tests were failing, I had to add the Context as a policy via boost::parameter. Similar to the other parameters in the state machine signature.

---

## Comment 2

> Username: chandryan  
> Created_at: 2025-10-09 21:13:21 UTC  
> Url: https://github.com/boostorg/msm/pull/101#issuecomment-3387507233  

I think it's better to support both the upper sm and context separately:  
  
The upper sm is a reference with an origin from within the state machine, while the context represents an externally provided dependency.  
By supporting both separately, we ensure the reference to the upper sm is always valid and doesn't need to be provided from outside, while the context only makes sense to be provided from outside the state machine.  
  
I incorporated a "root sm" (instead of "upper sm") setting in https://github.com/boostorg/msm/pull/124. The term "root sm" expresses that this sm is the uppermost sm in hierarchical state machines with multiple levels.

---
