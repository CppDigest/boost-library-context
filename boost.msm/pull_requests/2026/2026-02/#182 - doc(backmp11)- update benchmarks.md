# #182 doc(backmp11): update benchmarks [Merged]

> Username: chandryan  
> Created at: 2026-02-18 18:39:35 UTC  
> Updated at: 2026-02-18 18:45:44 UTC  
> Merged at: 2026-02-18 18:45:40 UTC  
> Closed at: 2026-02-18 18:45:40 UTC  
> Url: https://github.com/boostorg/msm/pull/182  

Update the benchmark tables in the backmp11 documentation with the results of a set of revised benchmarks.  
  
The results of the initial benchmarks seemed too far from reality. They triggered exit actions upon every state transition, a functionality which SML does not seem to be able to handle efficiently. They also enforced all internal transitions to be processed through the event queue/pool.  
  
The revised benchmark provides a better mix of state-orientied actions towards transition-oriented actions and the no. of   
events processed through the event queue/pool.

---
