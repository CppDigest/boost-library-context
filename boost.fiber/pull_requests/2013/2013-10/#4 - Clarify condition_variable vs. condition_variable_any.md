# #4 Clarify condition_variable vs. condition_variable_any. [Merged]

> Username: nat-goodspeed  
> Created at: 2013-10-31 15:27:25 UTC  
> Updated at: 2016-06-06 11:36:33 UTC  
> Merged at: 2013-11-02 05:45:36 UTC  
> Closed at: 2013-11-02 05:45:36 UTC  
> Url: https://github.com/boostorg/fiber/pull/4  

Make condition_variable::wait_until() and wait_for() consistently use 'lk' instead of 'lt' because explanations only describe 'lk'.  
Explain condition_variable::wait(), wait_until() and wait_for() Precondition.  
Distinguish values returned by wait_until(), wait_for() overloads with and without predicates.  
Update request for section concerning algorithm, set_scheduling_algorithm(), round_robin and round_robin_ws.  
Clarify lifespan implications of fiber_group::add_fiber(). Explain requirement for a heap fiber object.  
Experiment with cross-reference links to other Boost libraries.

---
