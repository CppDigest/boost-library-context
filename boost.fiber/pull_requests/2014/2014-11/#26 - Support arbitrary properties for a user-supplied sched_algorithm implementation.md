# #26 Support arbitrary properties for a user-supplied sched_algorithm implementation. [Merged]

> Username: nat-goodspeed  
> Created at: 2014-11-12 00:22:28 UTC  
> Updated at: 2016-06-06 11:36:33 UTC  
> Merged at: 2015-05-14 09:56:04 UTC  
> Closed at: 2015-05-14 09:56:04 UTC  
> Url: https://github.com/boostorg/fiber/pull/26  

Introduce fiber_properties base class + sched_algorithm_with_properties<PROPS>  
template base class. Introduce set_properties(), get_properties methods on  
worker_fiber; introduce fm_properties() functions for fiber_manager; introduce  
properties() methods on fiber, this_fiber and sched_algorithm_with_properties.  
  
Introduce sched_algorithm::property_change() method to support changes to  
properties that require immediate notification to the relevant sched_algorithm  
implementation -- such as a change in priority of a fiber already in that  
sched_algorithm's ready queue.  
  
Juggle namespaces so it's possible to implement a sched_algorithm subclass  
using only fiber_base\* (now promoted to boost::fibers namespace) rather than  
having to touch detail::worker_fiber*. Hoist nxt_ pointer from worker_fiber  
into fiber_base: we have many different needs (fortunately mutually exclusive)  
to put a fiber_base subclass on an intrusive linked list.  
  
In the migration example, introduce 'affinity' subclass of fiber_properties  
containing 'thread_affinity' data member. Reimplement workstealing_round_robin  
as a sched_algorithm_with_properties<affinity> subclass; tweak its steal()  
method to honor thread_affinity.  
  
Remove thread_affinity as a flag for every fiber, and the surprising number of  
support functions. Similarly, remove priority and its corresponding support  
functions. We could and probably should introduce an example like the  
migration example illustrating how one might implement a priority scheme, but  
I haven't yet tackled that.  
  
Simplify certain queue implementations using pointer-to-pointer tactics for  
the tail pointer and/or the scan pointer.  
  
(Please skim lightly over the .qbk edits: they are incomplete, and by now  
somewhat out-of-date. They got included only because the commit was still  
pending at the time I created the pull request.)

---
