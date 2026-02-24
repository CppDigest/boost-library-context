# #43 - Undocumented change of behaviour for events supplied to state machine during exception handling [Closed]

> Username: BlankSpruce  
> Created at: 2021-11-18 15:20:01 UTC  
> Updated at: 2024-07-23 16:07:24 UTC  
> Closed at: 2024-02-12 17:47:13 UTC  
> Url: https://github.com/boostorg/msm/issues/43  

Minified example:  
```cpp  
#include <boost/mpl/vector.hpp>  
#include <boost/msm/front/functor_row.hpp>  
#include <boost/msm/front/state_machine_def.hpp>  
#include <boost/msm/back/state_machine.hpp>  
#include <string>  
#include <iostream>  
#include <boost/version.hpp>  
  
template <typename T>  
void print(T t)  
{  
    std::cout << t << "\n";  
}  
  
template <typename... Ts>  
using Row = boost::msm::front::Row<Ts...>;  
  
using None = boost::msm::front::none;  
  
// events  
struct ExceptionRaised {};  
  
// states  
struct Init : boost::msm::front::state<> {};  
struct WaitingForException : boost::msm::front::state<> {};  
struct Intermediate : boost::msm::front::state<> {};  
struct End : boost::msm::front::terminate_state<> {};  
  
// actions  
struct ThrowingAction   
{  
    template <class Event, class StateMachine, class SourceState, class TargetState>  
    void operator()(const Event&, StateMachine&, SourceState&, TargetState&)  
    {  
        static int anti_looping_counter = 0;  
  
        if (anti_looping_counter++ > 4)  
        {  
            print("we're looping");  
            std::exit(1);  
        }  
  
        print("throw an exception");  
        throw std::runtime_error("foo");  
    }  
};  
struct ExceptionHandler   
{  
    template <class Event, class StateMachine, class SourceState, class TargetState>  
    void operator()(const Event&, StateMachine&, SourceState&, TargetState&)  
    {  
        print("handling an exception");  
    }  
};  
  
struct MyMachineFrontend : public boost::msm::front::state_machine_def<MyMachineFrontend>  
{  
using initial_state = boost::mpl::vector<WaitingForException, Init>;  
  
using transition_table = boost::mpl::vector<  
    Row<Init, None, Intermediate, ThrowingAction>,  
  
    Row<WaitingForException, ExceptionRaised, End, ExceptionHandler>  
>;  
  
template <class StateMachine, class Event>  
void exception_caught(const Event&, StateMachine& machine, std::exception&)  
{  
    print("exception caught");  
    machine.process_event(ExceptionRaised{});  
}  
};  
  
using MyMachine = boost::msm::back::state_machine<MyMachineFrontend>;  
  
int main()  
{  
    print(std::string("Boost version: ") + BOOST_LIB_VERSION);  
  
    MyMachine m;  
      
    m.start();  
}  
```  
In boost 1.71 we have:  
```  
Boost version: 1_71  
throw an exception  
exception caught  
handling an exception  
```  
Since boost 1.72 we have:  
```  
Boost version: 1_72  
throw an exception  
exception caught  
throw an exception  
exception caught  
throw an exception  
exception caught  
throw an exception  
exception caught  
throw an exception  
exception caught  
we're looping  
```  
  
[Here's this example seen in Godbolt.](https://godbolt.org/z/Y15dKbqfr)  
  
I don't see anything in the documentation that would disallow relying on this property and since it worked like that at least to version 1.71 I'd like to ask for clarification whether this is a bug or that behaviour post-1.72 is an intended one.

---

## Comment 1

> Username: davidfrulm  
> Created at: 2022-01-14 15:39:15 UTC  
> Updated at: 2022-01-14 15:41:45 UTC  
> Url: https://github.com/boostorg/msm/issues/43#issuecomment-1013232833  

Hi,  
  
I was actually going to write about the same issue. Digging a bit in the different msm versions [and in the back/state_machine.hpp header], the main difference I see in the example above is about when the completion event is executed in reference with the queued "ExceptionRaised" event.  
  
- In 1.71, queued events are executed first, then completion events are executed afterwards. In the example above:  
  
1. "ExceptionRaised" will be queued during the processing of the "start" event. Then the completion event is injected and tried to be processed... but because "m_event_processing" member variable is not yet reset, it is queued instead of being processed. Therefore, ExceptionEvent will be processed first.  
2.  After execution of "ExceptionRaised", FSM will transit to the "End" state, which inherits from "boost::msm::front::terminate_state<>" which, in turn, has the "::boost::msm::TerminateFlag". This makes any further event in the FSM to be silently discarded.   
3. So in this case, completion event is simply discarded and there is no loop.  
  
-  In later versions, order is reversed, meaning that completion events are executed first and then queued events are executed later. In the example above:  
  
1.   As before, "ExceptionRaised" will be queued during the processing of the "start" event. And as before, the completion is injected and tried to be processed. On later MSM versions, the "m_event_processing" would have been reset at that time, making that the completion event executes immediately; that's it BEFORE "ExceptionRaised".  
2. What will happen afterwards is that state that thrown the exception will receive the completion event, try to execute it and... throw again an exception...  
3.  Why the loop happens? Because the default return value in the try/catch in the exception handler in the MSM framework returns HANDLED_TRUE. That means that, for each completion event that the framework generates, FWK itself will acknowledge as HANDLED and then, because of that it will generate another completion event...  
4. If the above is accurate, it means that the infinite loop is generated during the handling of the exception by MSM framework itself.  
  
How to solve this is a matter about how to avoid generating further completion events . Some ideas:  
- Instead of returning "return HANDLED_TRUE;" in "do_process_helper", something like this can be used instead:  "return is_completion_event<EventType>::type::value ? HANDLED_FALSE : HANDLED_TRUE;". This will make that any non-completion event will keep same behavior as now... but any completion event generated by the framework will be declared as unhandled. This unhandled event won't generate further completion events.  
- Or maybe another option is to introduce a policy for the desired return value of the "do_process_helper". That policy might be global [all events which processing raised an exception"] or more targeted for completion events [which have no direct user control]  
  
I would appreciate some feedback in the issue and if/when would be possible to attempt a fix for it. Thanks

---

## Comment 2

> Username: davidfrulm  
> Created at: 2022-01-19 10:52:27 UTC  
> Updated at: 2022-01-19 10:58:21 UTC  
> Url: https://github.com/boostorg/msm/issues/43#issuecomment-1016318530  

I further looked into the changes introduced and I found new non-backwardscompatible changes beyond the exception handling one:  
  
Issue 1 on MSM 1_72+: Internal events generated as part of a completion event are consumed only after ALL completion events generated for initial transition have been completed.  
  
- Please find an example here: [Loop on "completion event" - simple FSM](https://godbolt.org/z/sehff6rxE)  
  
    using transition_table = boost::mpl::vector<  
        Row<Init        , None , Intermediate, None>,  
        Row<Intermediate, None , None        , SendDone, None>,  
        Row<Intermediate, Done , End         , None    , None>>;  
  
- In 1_69 internal events are handled before next “completion event”  [boost::none] is generated. So execution goes roughly as follows:  
-- Start -> transit to Intermediate  
-- New state / start consumed -> New boost::none generated and therefore “SendDone” is executed. At this point, “Done” is queued internally in the FSM.  
-- Once “boost::none” is consumed, internal event is consumed and we transit to End.   
  
- In 1_75 internal events are handled after   
-- Start -> transit to Intermediate  
-- New state / start consumed -> New boost::none generated and therefore “SendDone” is executed. At this point, “Done” is queued internally in the FSM.  
-- Once “boost::none” is consumed, another boost::none is generated.  
-- The new boost::none is consumed and another Done is queued… at this point we are looping  
-- As I understand this, the problem is that, in reality, we never finished the transition for the first  boost::none. Before triggering next “completion” event, we should completely process not only that “completion event” but any internal event generated from it.  
  
New Issue 2 on MSM 1_75: Just same case as before but extrapolated to composite state machines  
- Please find an example here: [Loop on "completion event" - composite FSM](https://godbolt.org/z/j397x56ET)  
- Unfortunately in this case I found no way of illustrate the loop in meaningful manner; no matter where I put the “we are looping” log. Only comparison we have here is that in 1_69 example goes fine but in 1_75 it crashes.  
- Interworking between parent/child FSMs in the MSM framework is something that escapes my current understanding. For now, I can only make a polite guess that this and previous points are related but I can’t precisely ping down the details.

---

## Comment 3

> Username: henry-ch  
> Created at: 2024-02-12 17:47:13 UTC  
> Url: https://github.com/boostorg/msm/issues/43#issuecomment-1939231710  

Sorry about the undocumented breaking change. In 1.72, the implementation of the event queue has changed, but it should not have been a breaking one, hence undocumented.  
However, the implementation regarding completion events is correct. The UML Standard states that completion events have the highest priority, so that the first transition is tried again and again.  
  
Still, I understand endless looping due to exception is annoying. A way to do as the Standard says and avoid this behavior is to consider the transition as "not handled" in case of an exception, in which case the completion events will not be tried again and the loop ends.  
The output now is exactly as it was before.

---

## Comment 4

> Username: davidfrulm  
> Created at: 2024-04-24 15:56:32 UTC  
> Url: https://github.com/boostorg/msm/issues/43#issuecomment-2075288111  

I kind of disagree on the outcome of this ticket.  
  
Let's come back to he issue as described here: https://godbolt.org/z/EoxqPcdGP  
  
#include <boost/mpl/vector.hpp>  
#include <boost/msm/front/functor_row.hpp>  
#include <boost/msm/front/state_machine_def.hpp>  
#include <boost/msm/back/state_machine.hpp>  
#include <string>  
#include <iostream>  
#include <boost/version.hpp>  
  
template <typename T>  
void print(T t)  
{  
    std::cout << t << "\n";  
}  
  
template <typename... Ts>  
using Row = boost::msm::front::Row<Ts...>;  
  
using None = boost::msm::front::none;  
  
// events  
struct Done{};  
  
// states  
struct Init : boost::msm::front::state<>{};  
struct Intermediate : boost::msm::front::state<>  
{  
        template <class Event, class StateMachine>  
    void on_entry(const Event&, StateMachine& stateMachine)  
    {  
            print("on_entry: Intermediate");  
    }  
  
    template <class Event, class StateMachine>  
    void on_exit(const Event&, StateMachine& stateMachine)  
    {  
            print("on_exit: Intermediate");  
    }  
};  
struct End : boost::msm::front::state<>{};  
  
struct MyMachineFrontend : public boost::msm::front::state_machine_def<MyMachineFrontend>  
{  
    using initial_state = Init;  
  
    struct SendDone  
    {  
        template <class Event, class StateMachine, class SourceState, class TargetState>  
        void operator()(const Event&, StateMachine& machine, SourceState&, TargetState&)  
        {  
            static int anti_looping_counter = 0;  
  
            if (anti_looping_counter++ > 4)  
            {  
                print("we're looping");  
                std::exit(1);  
            }  
  
            print("Intermediate is Done");  
            machine.process_event(Done{});  
        }  
    };  
  
    using transition_table = boost::mpl::vector<  
        Row<Init        , None , Intermediate, None>,  
        Row<Intermediate, None , None        , SendDone, None>,  
        Row<Intermediate, Done , End         , None    , None>  
    >;  
};  
using MyMachine = boost::msm::back::state_machine<MyMachineFrontend>;  
  
int main()  
{  
    print(std::string("Boost version: ") + BOOST_LIB_VERSION);  
  
    MyMachine m;  
      
    m.start();  
}  
  
Output in 1_71:  
Boost version: 1_71  
on_entry: Intermediate  
Intermediate is Done  
on_exit: Intermediate  
  
Output in 1_72:  
Boost version: 1_72  
on_entry: Intermediate  
Intermediate is Done  
Intermediate is Done  
Intermediate is Done  
Intermediate is Done  
Intermediate is Done  
we're looping  
  
This is a 100% non-backwards compatible change for auto-transitions. My polite guess is that this should have been added as configurable option and not as the default.  
  
So is there a possibility to re-introduce previous behaviour but being able to configure alternate policies for auto-transitions through some configurable option?  
  
Right now, in my current project, we are unable to use newest versions of boost::msm. And for future projects, because this change, we are currently not considering boost::msm in favor of other alternatives.

---

## Comment 5

> Username: henry-ch  
> Created at: 2024-05-27 15:27:28 UTC  
> Updated at: 2024-05-27 16:06:51 UTC  
> Url: https://github.com/boostorg/msm/issues/43#issuecomment-2133697253  

We are now talking about something completely different! The issue title is:  
"Undocumented change of behaviour for events supplied to state machine during exception handling". Your code now has no more exception, so it is a different issue.  
The problem is that your FSM was wrong in the first place. According to the UML Standard, completion events (None) have higher priority, so that this transition:  
`Row<Intermediate, None , None        , SendDone, None>`  
clearly is an infinite loop. Probably, the old MSM implementation, being recursive, was handling this like you wanted, but it was wrong.  
I suggest you fix the state machine using a guard in this transition.  
What you are asking is that MSM implements an incorrect UML behaviour. I do not know if that is possible, but it would a) cost compile time and b) not be the default.  
I disagree with the idea that breaking an incorrect state machines is a breaking change.

---

## Comment 6

> Username: davidfrulm  
> Created at: 2024-05-28 13:44:35 UTC  
> Url: https://github.com/boostorg/msm/issues/43#issuecomment-2135256926  

Some extra notes:  
  
1) Please take my comment below with a grain of salt since my initial analysis on this topic was done more than 2,5 years ago...   
  
But my understanding about the fundamental change introduced by the "new" version of boost::msm is how internally generated events are processed in regard of the currently executed transition.   
  
I have the impression that "before", any event internally generated as part of the current transition, was considered to belong to "same" transition. Only when all internally generated events were consumed [and the initial transition was executed "to completion"], the auto transition was evaluated.  
  
I have never considered that interpretation of the standard as inherently wrong. It has some pros/cons by itself. So I am rather disappointed to see that interpretation out-ruled with no backward compatibility.   
  
2) About the transformation between a recursive piece of code into an iterative one: These kind of transformations can be done without changing the behaviour of the code.  Whatever the reason for the behaviour change, it can be detached from the recursive-to-iterative transformation.  
  
3) About changing our source code and adapt to new version:  
* It is expensive enough for not being considered as first choice.   
* A lot of trust has been lost after the reported changes in boost::msm. Now, it can't be ruled out that further non-backward compatible changes could happen again.

---

## Comment 7

> Username: davidfrulm  
> Created at: 2024-07-23 16:07:23 UTC  
> Url: https://github.com/boostorg/msm/issues/43#issuecomment-2245654048  

Ok, lets backtrack a bit to the exception handling problem and to a previous proposal up in this thread.  
  
How feasible would be to add a return value to "exception_caught" so that we could decide if event is actually considered "handled"?  
  
    template <class StateMachine, class Event>  
    **HandledEnum** exception_caught(Event const& event, StateMachine& stateMachine, std::exception const& e)  
    {  
        ...  
    }  
      
That would allow clients to custom define how to treat exceptions for certain events.
