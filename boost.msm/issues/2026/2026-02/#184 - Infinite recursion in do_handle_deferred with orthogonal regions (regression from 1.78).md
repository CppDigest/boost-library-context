# #184 - Infinite recursion in do_handle_deferred with orthogonal regions (regression from 1.78) [Open]

> Username: nikkikom  
> Created at: 2026-02-18 23:21:19 UTC  
> Updated at: 2026-02-19 21:24:00 UTC  
> Url: https://github.com/boostorg/msm/issues/184  

## Infinite recursion in do_handle_deferred with orthogonal regions (regression from 1.78)  
  
### Environment  
  
- **Boost version:** 1.86.0 and later (works correctly in 1.78.0)  
- **Compiler:** GCC 14.2.0 (also reproducible with Clang 18)  
- **OS:** Linux x86_64  
  
### Description  
  
`do_handle_deferred` enters infinite recursion when a state machine has orthogonal  
regions and one region defers an event while another region handles the same event  
with an internal transition. This causes a stack overflow / segfault.  
  
The bug is a regression introduced between Boost 1.78 and 1.86 in the "retry on success" logic added to `do_handle_deferred` in `boost/msm/back/state_machine.hpp`.  
  
### Minimal reproducer  
  
```cpp  
#include <boost/msm/back/state_machine.hpp>  
#include <boost/msm/front/functor_row.hpp>  
#include <boost/msm/front/state_machine_def.hpp>  
#include <cstdio>  
  
namespace msm = boost::msm;  
namespace mpl = boost::mpl;  
using namespace msm::front;  
  
struct Ping {};  
  
struct Machine_ : state_machine_def<Machine_>  
{  
    typedef int activate_deferred_events;  
  
    struct Waiting : state<> {};  
    struct Ready   : state<> {};  
    struct Sink    : state<> {};  
  
    // Two orthogonal regions  
    typedef mpl::vector<Waiting, Sink> initial_state;  
  
    struct transition_table : mpl::vector<  
        //        Source   Event  Target  Action  Guard  
        Row<      Waiting, Ping,  none,   Defer,  none>,  // region 1: defer  
        Row<      Sink,    Ping,  none,   none,   none>   // region 2: handle (discard)  
    > {};  
  
    template <class FSM, class Event>  
    void no_transition(Event const&, FSM&, int) {}  
};  
  
typedef msm::back::state_machine<Machine_> Machine;  
  
int main()  
{  
    Machine sm;  
    sm.start();  
    std::puts("Processing Ping...");  
    sm.process_event(Ping{});   // <-- stack overflow here  
    std::puts("Done.");  
}  
```  
  
- Boost 1.78: prints "Done." and exits normally.  
- Boost 1.86: segfaults (stack overflow from ~30,000 recursive frames in do_handle_deferred).  
  
### Root cause  
  
In Boost 1.78, do_handle_deferred simply iterates through the deferred queue and fires each event. Re-deferred events get a higher sequence number and are naturally skipped by the cur_seq != pair.second check:  
  
```cpp  
// Boost 1.78 — state_machine.hpp:1891  
void do_handle_deferred(bool new_seq=false)  
{      
    if (new_seq) ++m_events_queue.m_cur_seq;      
    char& cur_seq = m_events_queue.m_cur_seq;  
    while (!m_events_queue.m_deferred_events_queue.empty())  
    {  
        auto& pair = m_events_queue.m_deferred_events_queue.front();  
        if (cur_seq != pair.second) break;  
        deferred_fct next = pair.first;  
        m_events_queue.m_deferred_events_queue.pop_front();  
        next();   // return value ignored — safe  
    }  
}  
```  
  
Boost 1.86 added logic to check the return value and recursively retry when an event was "successfully processed":  
  
```cpp  
// Boost 1.86 — state_machine.hpp:1971  
void do_handle_deferred(bool new_seq=false)  
{  
    if (new_seq) ++m_events_queue.m_cur_seq;  
    char& cur_seq = m_events_queue.m_cur_seq;  
      
    bool not_only_deferred = false;  
    while (!m_events_queue.m_deferred_events_queue.empty())  
    {  
        auto& pair = m_events_queue.m_deferred_events_queue.front();  
        if (cur_seq != pair.second) break;  
  
        deferred_fct next = pair.first;  
        m_events_queue.m_deferred_events_queue.pop_front();   
        boost::msm::back::execute_return res = next();   
          
        if (res != HANDLED_FALSE && res != HANDLED_DEFERRED)  // <-- BUG  
            not_only_deferred = true;  
        if (not_only_deferred)  
            break;  
    }  
    if (not_only_deferred)    {  
        std::stable_sort(/* ... */);  
        std::for_each(/* ... */, set_sequence(m_events_queue.m_cur_seq + 1));  
        do_handle_deferred(true);   // <-- infinite recursion  
    }  
}  
```  
  
The check on line 2001 uses equality:  
  
`res != HANDLED_DEFERRED  
`  
  
With orthogonal regions, region_processing_helper::In::process OR's the result from each region (line 2140):  
  
`result_ = (HandledEnum)((int)result_ | (int)res);  
`  
  
When region 1 defers (HANDLED_DEFERRED = 2) and region 2 handles (HANDLED_TRUE = 1), the combined result is HANDLED_TRUE | HANDLED_DEFERRED = 3. The equality check 3 != HANDLED_DEFERRED(2) evaluates to true, so the code incorrectly concludes the event was successfully processed. It resets the sequence numbers of remaining deferred events and calls do_handle_deferred(true) recursively. The recursive call increments cur_seq to match the re-deferred event, replays it, gets the same OR'd result, and recurses again - infinitely.  
  
### Suggested fix  
  
Change the equality check to a bitmask check on line 2001 of boost/msm/back/state_machine.hpp:  
  
```cpp  
// Before (broken for multi-region OR'd results):  
if (res != ::boost::msm::back::HANDLED_FALSE && res != ::boost::msm::back::HANDLED_DEFERRED)  
```  
  
```cpp  
// After (correctly detects HANDLED_DEFERRED bit in OR'd results):  
if (res != ::boost::msm::back::HANDLED_FALSE && !(res & ::boost::msm::back::HANDLED_DEFERRED))  
```  
  
This correctly identifies that the event was re-deferred (even when another region also handled it), preventing the recursive retry.

---

## Comment 1

> Username: chandryan  
> Created at: 2026-02-19 21:09:37 UTC  
> Updated at: 2026-02-19 21:24:00 UTC  
> Url: https://github.com/boostorg/msm/issues/184#issuecomment-3930054022  

Thanks for the suggested fix, I looked into it and [created a test case](https://github.com/chandryan/msm/blob/fix/back-orthogonal-handled-and-deferred/test/OrthogonalDeferred4.cpp).  
While it does prevent a segfault, the behavior of the state machine is still not as it should be: A potential action in your second orthogonal region is called one times too many in my test case.  
  
For reference, we discussed this bug in #72. Below are possible options for you to either work around or fix the issue:  
  
**Workaround:**  
  
Use a guard like I did in the test case to change your region's behavior from `handle (discard)` to `do not handle (reject)` (see also the [MSM docu](https://www.boost.org/doc/libs/latest/doc/antora/msm/tutorial/back-end.html#_deferring_events_in_orthogonal_regions)).  
  
Alternatively you can use the newer `backmp11` back-end without a guard, in which you wouldn't observe the segfault but might see a different processing order because it does not attempt to re-order deferred events. However, you would still observe unexpected behavior like duplicate event processing.  
  
IMHO "action-deferrals" and orthogonal regions simply don't go together well (see the discussion in #72 for more details).  
  
**Fix:**  
  
Use the newer `backmp11` back-end and set `Ping` as deferred event of `Waiting` with the `deferred_events` property.  
  
The `deferred_events` property in `backmp11` supports orthogonal regions as defined in the UML specification, which means: If any active state, no matter in which region, decides to defer an event, it is and remains deferred until no more active state decides to defer it.  
It also recently gained the [ability to defer conditionally](https://www.boost.org/doc/libs/develop/doc/antora/msm/tutorial/backmp11-back-end.html#_support_for_conditional_event_deferral_with_the_deferred_events_property), which is already available on master and will be released with Boost 1.91.  
  
Does any of these options suit your needs?
