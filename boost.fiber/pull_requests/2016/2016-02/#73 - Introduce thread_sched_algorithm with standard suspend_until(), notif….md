# #73 Introduce thread_sched_algorithm with standard suspend_until(), notif… [Closed]

> Username: nat-goodspeed  
> Created at: 2016-02-15 22:56:36 UTC  
> Updated at: 2016-06-06 11:36:34 UTC  
> Closed at: 2016-03-23 07:21:18 UTC  
> Url: https://github.com/boostorg/fiber/pull/73  

…y().  
  
round_robin, along with pretty much every example program's scheduler except  
examples/asio/round_robin.hpp, uses the same suspend_until() / notify()  
implementation. It's not a stretch to assume that most Fiber applications that  
customize sched_algorithm at all will use the very same implementations as  
well. Instead of forcing them to copy/paste from round_robin, allow them to  
derive from thread_sched_algorithm.

---

## Comment 1

> Username: nat-goodspeed  
> Created_at: 2016-02-21 16:35:25 UTC  
> Url: https://github.com/boostorg/fiber/pull/73#issuecomment-186855227  

Using thread_sched_algorithm along with sched_algorithm_with_properties<T> requires virtual inheritance so there's only one copy of the sched_algorithm base class in the inheritance diagram. If there are multiple copies, one or more of them will complain about unsatisfied pure virtual methods.

---

## Comment 2

> Username: olk  
> Created_at: 2016-02-21 18:14:07 UTC  
> Url: https://github.com/boostorg/fiber/pull/73#issuecomment-186876083  

priority.cpp:91:18: warning: 'priority_scheduler::awakened' hides overloaded virtual function [-Woverloaded-virtual]  
    virtual void awakened( boost::fibers::context \* ctx, priority_props & props) noexcept {  
                 ^  
../../../boost/fiber/algorithm.hpp:33:18: note: hidden overloaded virtual function 'boost::fibers::sched_algorithm::awakened' declared here: different number of parameters (1 vs 2)  
    virtual void awakened( context *) noexcept = 0;

---

## Comment 3

> Username: nat-goodspeed  
> Created_at: 2016-02-21 19:19:36 UTC  
> Url: https://github.com/boostorg/fiber/pull/73#issuecomment-186894098  

On Feb 21, 2016 1:14 PM, "Oliver Kowalke" notifications@github.com wrote:  
  
> priority.cpp:91:18: warning: 'priority_scheduler::awakened' hides  
> overloaded virtual function [-Woverloaded-virtual]  
> virtual void awakened( boost::fibers::context \* ctx, priority_props &  
> props) noexcept {  
> ^  
> ../../../boost/fiber/algorithm.hpp:33:18: note: hidden overloaded virtual  
> function 'boost::fibers::sched_algorithm::awakened' declared here:  
> different number of parameters (1 vs 2)  
> virtual void awakened( context *) noexcept = 0;  
  
Interesting! I don't see that with GCC 4.9.3 on Ubuntu 14.04.  
  
sched_algorithm declares awakened(context_) as pure virtual.  
thread_sched_algorithm does not provide an awakened() override.  
sched_algorithm_with_properties_base doesn't either.  
sched_algorithm_with_properties<T> provides awakened(context_) as 'final'  
and introduces awakened(context_, T&) as pure virtual.  
priority_scheduler overrides awakened(context_, priority_props&).  
  
Given the 'final' declaration on  
sched_algorithm_with_properties<T>::awakened(context_), I don't care that  
priority_scheduler::awakened(context_, priority_props&) hides  
awakened(context*).  
  
Is there some syntax I should use to reassure the compiler that things are  
as they should be?

---

## Comment 4

> Username: nat-goodspeed  
> Created_at: 2016-02-21 19:34:55 UTC  
> Url: https://github.com/boostorg/fiber/pull/73#issuecomment-186895658  

On Feb 21, 2016 2:19 PM, "Nat Goodspeed" nat@lindenlab.com wrote:  
  
> Is there some syntax I should use to reassure the compiler that things  
> are as they should be?  
  
'override' keyword?

---

## Comment 5

> Username: olk  
> Created_at: 2016-02-22 18:48:30 UTC  
> Url: https://github.com/boostorg/fiber/pull/73#issuecomment-187314824  

gcc 5.2.1 causes the warnings

---

## Comment 6

> Username: olk  
> Created_at: 2016-03-23 07:21:18 UTC  
> Url: https://github.com/boostorg/fiber/pull/73#issuecomment-200226733  

Because this patch conflicts with the actual code I'll close it unmerged.

---
