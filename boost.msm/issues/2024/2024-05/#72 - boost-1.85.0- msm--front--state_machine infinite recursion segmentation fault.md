# #72 - boost/1.85.0: msm::front::state_machine infinite recursion segmentation fault [Closed]

> Username: hoyhoy  
> Created at: 2024-05-05 16:50:44 UTC  
> Updated at: 2025-12-19 14:13:06 UTC  
> Closed at: 2025-12-19 14:13:06 UTC  
> Url: https://github.com/boostorg/msm/issues/72  

boost::msm::back::state_machine is now going into an infinite recursion and crashing. Worked in boost/1.84.0.    
  
Segmentation fault occurring on clang 18.1 and gcc 13.1, works on latest MSVC (17.9.6) though.  
  
```  
#101978 0x0000000000890a0c in boost::msm::back::state_machine<msm_front, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>::handle_defer_helper<boost::msm::back::state_machine<msm_front, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>, int>::do_handle_deferred (this=0x7ffff309fcc0, new_seq=0x1) at include/boost/msm/back/state_machine.hpp:2028  
#101979 0x0000000000890a0c in boost::msm::back::state_machine<msm_front, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>::handle_defer_helper<boost::msm::back::state_machine<msm_front, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>, int>::do_handle_deferred (this=0x7ffff309fcc0, new_seq=0x1) at include/boost/msm/back/state_machine.hpp:2028  
#101980 0x0000000000890a0c in boost::msm::back::state_machine<msm_front, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>::handle_defer_helper<boost::msm::back::state_machine<msm_front, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>, int>::do_handle_deferred (this=0x7ffff309fcc0, new_seq=0x1) at include/boost/msm/back/state_machine.hpp:2028  
#101981 0x0000000000890a0c in boost::msm::back::state_machine<msm_front, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>::handle_defer_helper<boost::msm::back::state_machine<msm_front, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>, int>::do_handle_deferred (this=0x7ffff309fcc0, new_seq=0x1) at include/boost/msm/back/state_machine.hpp:2028  
#101982 0x0000000000890a0c in boost::msm::back::state_machine<msm_front, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>::handle_defer_helper<boost::msm::back::state_machine<msm_front, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>, int>::do_handle_deferred (this=0x7ffff309fcc0, new_seq=0x1) at include/boost/msm/back/state_machine.hpp:2028  
```

---

## Comment 1

> Username: hoyhoy  
> Created at: 2024-05-07 14:55:19 UTC  
> Updated at: 2024-05-07 15:26:02 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-2098605044  

@ChristophStrehle there are breaking changes in boost/1.85.0.  MSM now processes deferred events and the event table differently than boost/1.84.0.  Both clang and gcc segmentation fault with our codebase written in 2017.  
  
We have an event that is changing state and adding a deferred event and that caused an infinite loop.  I just refactored our code to prevent the state change by deferring the second event rather than moving to the next state.  The behavior of the msm changed between 1.84.0 and 1.85.0 for us.  
  
```  
Row < Running, Stop,    Stopped, Stop_cmd>  
Row < Running, Close,   Internal, Stop_and_close>  
```  
  
If a `Shutdown` event occurs, our `Stop_and_close` defers a `Close` and processes a `Stop`, but then `handle_defer_helper()` causes another `Stop_and_close` which defers another `Close` which causes another deferred `Stop_and_close` to occur which is infinite recursion and a segmentation fault.  
  
```  
    template <class FSM,class SourceState,class TargetState>  
    void operator()(Event::Close const& ev,FSM& fsm,  
        SourceState& , TargetState& )  
    {  
        fsm.process_event( Event::Stop() );  
        fsm.defer_event( ev );  
    }  
```  
  
We should be in the `Internal` state after `Close`.  It looks like a bug to me.

---

## Comment 2

> Username: ChristophStrehle  
> Created at: 2024-05-13 06:27:59 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-2106750025  

Hello @hoyhoy, unfortunately I'm not so familiar with the implementation of the msm state machine. From the callstack and the description you provided I would guess that the change in behavior is caused by the commit f37d4a88f1dfd9821859d2f3f2f91b3b2b6a3cc8 from @henry-ch.

---

## Comment 3

> Username: henry-ch  
> Created at: 2024-05-27 15:18:42 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-2133684345  

Hello @hoyhoy,  
could you provide me with a minimum example? It is possible that I induced a new bug while fixing one, it might also be possible that the fsm was wrong.

---

## Comment 4

> Username: hoyhoy  
> Created at: 2024-05-30 16:28:27 UTC  
> Updated at: 2024-05-30 23:17:05 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-2140167081  

Yeah, it's mega broken for us right now.   Whatever was done post 1.83 seemed to break one of our applications.  
  
Also, this started happening as well...  
https://developercommunity.visualstudio.com/t/cl-193933523-x64-boost1840-boost/10622335  
  
We can't compile on MSVC, and MSM no longer change states in the way all releases prior to Boost 1.85.0 used to.  
  
I posted a minimum repro above.  Basically, if you defer an event, Boost 1.85 changed how the state machine worked.  The bug is, `handle_defer_helper()` is changing the next state when it didn't do that previously.

---

## Comment 5

> Username: henry-ch  
> Created at: 2024-07-12 14:47:09 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-2225752199  

I cannot understand the problem with the above code. Sorry, "mega broken" is not helpful. Please provide a complete example with the whole state machine. Guessing is usually not a good idea.  
I cannot reproduce the compiler crash with VS 17.8 (and Microsoft also not from what I see). Which compiler are you using?  
Please provide also there a complete example, if possible, I will try to find the compiler you are using.

---

## Comment 6

> Username: shingst  
> Created at: 2025-10-31 09:54:57 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-3472218588  

The bug does still happen on the latest commit, tested with all three backends.  
  
Minimal example:  
```  
#include <boost/msm/back/state_machine.hpp>  
#include <boost/msm/front/completion_event.hpp>  
#include <boost/msm/front/state_machine_def.hpp>  
#include <boost/msm/front/functor_row.hpp>  
  
namespace msm = boost::msm;  
namespace mpl = boost::mpl;  
using namespace boost::msm::front;  
  
namespace  
{  
    struct Event1 {};  
    struct Event2 {};  
  
    struct SM_ : public state_machine_def<SM_>  
    {  
        struct State1 : public state<> {};  
  
        struct State2 : public state<>  
        {  
            typedef mpl::vector<Event1> deferred_events;  
        };  
  
        struct State3 : public state<> {};  
  
        struct action1   
        {   
            template <class Fsm,class Evt,class SourceState,class TargetState>   
            void operator()(Evt const&, Fsm&, SourceState&, TargetState& )   
            {  
            }   
        };   
  
        struct State4 : public state<> {  
            struct internal_transition_table : public mpl::vector<  
            //        Event   | Action  
            // ---------------+---------------------------  
            Internal< Event1  , action1 >  
            > {};  
        };  
  
        typedef mpl::vector<State1,State3> initial_state;  
  
        struct transition_table : mpl::vector<  
            //      Start     Event         Next      Action               Guard  
            //    +---------+-------------+---------+---------------------+----------------------+  
            Row   < State1  , Event1      , State2  , none                           >,  
            //    +---------+-------------+---------+---------------------+----------------------+  
            Row   < State3  , Event1      , State4  , none                           >  
            // ,Row  < State4  , Event1      , none    , action1                        >    
            //    +---------+-------------+---------+---------------------+----------------------+  
        > {};  
  
    };  
    typedef msm::back::state_machine<SM_> SM;  
}  
  
int main()  
{  
  SM p;  
  p.start();  
  p.process_event(Event1());  
  p.process_event(Event1());// <- Event1 is processible in State4 and deferred in State2  
  p.stop();  
}  
```  
The infinite recursion does also happen if we remove the internal transition table and use the commented Row instead.  
Both examples work with boost 1.84.  
  
tested with clang-18, gcc-11, gcc-14.  
relevant part of the stack-trace:  
  
```  
    frame #29115: 0x000055555555a19c tuple`boost::msm::back::state_machine<(anonymous namespace)::SM_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>::handle_defer_helper<boost::msm::back::state_machine<(anonymous namespace)::SM_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>, int>::do_handle_deferred(this=0x00007fffffffe550, new_seq=true) at state_machine.hpp:2013:17  
    frame #29116: 0x000055555555a19c tuple`boost::msm::back::state_machine<(anonymous namespace)::SM_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>::handle_defer_helper<boost::msm::back::state_machine<(anonymous namespace)::SM_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>, int>::do_handle_deferred(this=0x00007fffffffe550, new_seq=true) at state_machine.hpp:2013:17  
    frame #29117: 0x000055555555a19c tuple`boost::msm::back::state_machine<(anonymous namespace)::SM_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>::handle_defer_helper<boost::msm::back::state_machine<(anonymous namespace)::SM_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>, int>::do_handle_deferred(this=0x00007fffffffe550, new_seq=true) at state_machine.hpp:2013:17  
    frame #29118: 0x0000555555557dcd tuple`boost::msm::back::state_machine<(anonymous namespace)::SM_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>::do_handle_prio_msg_queue_deferred_queue(this=0x00007fffffffe5f8, source='\x01', handled=HANDLED_TRUE | HANDLED_DEFERRED, (null)=0x00007fffffffe58c) at state_machine.hpp:1838:26  
    frame #29119: 0x0000555555557ae1 tuple`boost::msm::back::HandledEnum boost::msm::back::state_machine<(anonymous namespace)::SM_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>::process_event_internal<(anonymous namespace)::Event1>(this=0x00007fffffffe5f8, evt=0x00007fffffffe5ea, source='\x01') at state_machine.hpp:2194:13  
    frame #29120: 0x00005555555559a2 tuple`boost::msm::back::HandledEnum boost::msm::back::state_machine<(anonymous namespace)::SM_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>::process_event<(anonymous namespace)::Event1>(this=0x00007fffffffe5f8, evt=0x00007fffffffe5ea) at state_machine.hpp:1255:16  
    frame #29121: 0x00005555555556fa tuple`main at stackoverflow.cpp:63:5  
```

---

## Comment 7

> Username: hoyhoy  
> Created at: 2025-10-31 16:21:45 UTC  
> Updated at: 2025-10-31 16:37:32 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-3473845386  

> The bug does still happen on the latest commit, tested with all three backends.  
  
We had to refactor our msm implementation to defer an event instead of process.  That avoided the infinite loop.  
  
The bug is that `handle_defer_helper` is changing the to the next state when it didn't do that in boost/1.84.0.  This is causing an infinite loop.  Still not fixed over a year later.

---

## Comment 8

> Username: chandryan  
> Created at: 2025-10-31 18:06:47 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-3474240501  

> Still not fixed over a year later.  
  
Allow me to reflect my observations on your behavior:  
  
You are using a free-of-charge OSS library that is provided as-is without warranty of any kind, developed and maintained by a community of volunteers in their free time.  
For which you reported a bug and have been requested, over a year ago, to provide a complete example to reproduce your bug. Which you haven't done.  
  
And deriving from a statement such as "still not fixed", I observe that you seem you think you have a right to expect that your bug should have been fixed in the meantime, implicitly complaining about the same not having happened yet.  
  
I strongly advise you to reconsider what you can and cannot expect to happen in free-of-charge OSS libraries maintained by volunteers, as well as what can be expected from you when you report a bug.  
And I strongly advise you to reconsider how a mutually respectful communication style looks like. This will help both parties minimize pointless agitation and frustration.  
  
---  
  
Thanks to @shingst's complete example there now is a base for further investigations into this matter.

---

## Comment 9

> Username: hoyhoy  
> Created at: 2025-10-31 18:27:54 UTC  
> Updated at: 2025-10-31 18:36:57 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-3474346579  

Allow me to reflect on your behavior -- I stated _exactly_ what the bug in msm was over a year ago i.e. `handle_defer_event()` is changing to the next state in 1.85, whereas in 1.84 it didn’t.  
  
I’m not expecting anything other than for msm to _not_ randomly merge breaking changes without a deprecation notice.  
  
I ended up spending several hours patching some ancient software (that I didn't write) to engineer out this dependency because I assumed (rightly) that boost::msm doesn't have unit test coverage around this behavior.  So, nobody would know it was broken until it was released.

---

## Comment 10

> Username: chandryan  
> Created at: 2025-11-02 20:39:45 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-3478283298  

> Allow me to reflect on your behavior -- I stated _exactly_ what the bug in msm was over a year ago i.e. `handle_defer_event()` is changing to the next state in 1.85, whereas in 1.84 it didn’t.  
  
While you are very welcome to share suggestions about where the root cause might be, you were explicitly requested to share a complete example for reproducing the issue. Looking through the conversation within this issue, your missing response seems to be the main reason this bug has not been worked on for such a long time.  
  
And if you happen to know _exactly_ what the bug is, you are always invited to create a PR containing the bugfix and a regression test to contribute to the library.  
  
> I’m not expecting anything other than for msm to _not_ randomly merge breaking changes without a deprecation notice.  
  
You _can_ expect library developers to _not_ do something like this intentionally.  
You _cannot_ expect them to not accidentally break something.  
   
> [...] because I assumed (rightly) that boost::msm doesn't have unit test coverage around this behavior. So, nobody would know it was broken until it was released.  
  
You _can_ expect library developers to follow Best Practices and add regression tests for identified & resolved bugs.   
You _cannot_ expect an OSS library, or any piece of software in this regard, to have unit tests covering all possible cases.  
  
---  
  
I commented about your statement in this issue in order to motivate you to reflect about how your behavior can be perceived by others, especially the ones you are asking for support.  
  
You complaining to developers about an *accidentally* broken functionality in an OSS lib, which you can use free-of-charge, brings nothing but frustration to all involved parties. What do you get out of this? Best case you were able to get off some steam at the cost of agitating others, worst case you demotivate the people you are asking for support to look into your problem.  
  
I'm asking you no more than to consider how your behavior affects others, because nonviolent & respectful communication is essential - especially in community-driven OSS projects. And statements such as "mega broken", "still not fixed over a year later" or "randomly merge breaking changes" are simply inappropriate and will not help anyone.  
  
Take it or leave it, I will not comment further on your behavior.

---

## Comment 11

> Username: hoyhoy  
> Created at: 2025-11-02 21:04:11 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-3478334951  

You're right, I should have provided an example, but it would have taken me hours to do that.  Our msm code is buried in a  closed-source app.  
  
I just refactored out our reliance on this  `handle_defer_helper()` behavior.  This was one problem of about 5,000 problems we faced when updating our dependencies last year.

---

## Comment 12

> Username: chandryan  
> Created at: 2025-11-05 21:50:17 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-3493660457  

I looked into the background of the implementation change, it was related to #18. As far as I understand, it attempts to retain the correct order of deferred events in the queue. The algorithm goes through all deferred events in the queue and sorts it, whenever an event was successfully processed. The recently appended items go back to the front of the queue and then the algorithm starts from the beginning.  
  
This algorithm goes into infinite recursion because of the orthogonal regions in your example: The event is processed and deferred at the same time, which means it goes back into the queue but also sorted, and then processed again and again.   
  
I have [one idea in mind](https://github.com/chandryan/msm/pull/14/files) how to remove the infinite recursion and probably even retain the correct event order in *some* cases, but this feels more like a makeshift solution. That idea relies on the assumption, that at max. 1 region is deferring the event. If that algorithm were applied in a situation where both regions defer the event, the sorting would not work anymore.   
  
In general I cannot think of a fully working implementation for event deferral, if orthogonal regions are involved and the event has to be dispatched for evaluation. I don't know what your use case is @shingst , but there is a high chance that even without infinite recursion the deferral might not do what you want:  
In your example Event1 gets consumed and deferred at the same time. As soon as State2 is not active anymore, State4 would consume the same Event1 a second time.  
Other side effects would come into place, if you had a situation where both regions defer the event; its occurrences in the deferred events queue would increase with every attempt to process them.  
  
I would like to look into a UML-compliant solution for the new back-end `backmp11`, which is described in #143. Unfortunately I don't see a way to implement the same in the older back-ends, the reasons for this are also described in #143.  
  
I'm not sure how to go forward with `back` and `back11`, I see the following possible options:  
- Go with the makeshift solution to prevent infinite recursion and have the correct event order in *some* cases,  
- Revert the implementation to the previous state without the attempt to sort,  
- Or find some other solution that I cannot see.  
  
What's your opinion @henry-ch ?

---

## Comment 13

> Username: henry-ch  
> Created at: 2025-11-13 17:29:57 UTC  
> Updated at: 2025-11-13 17:39:05 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-3528923636  

I think the state machine is wrong. One region is constantly deferring the event again and again unconditionally.  
The first region defers, while the second one processes it, then a copy of the first event is processed and the original region defers, etc.  
I cannot think of a way to make it work at the moment. I could remember the region which defers the event and not re-defer, but then it is 99% sure I will break somebody else's code. I need to think if I can solve generally, but I think simply conditionally deferring using a guard is a much more stable solution.  
  
I re-read the standard and https://github.com/boostorg/msm/issues/143 quotes the right place of the Standard. Events remain deferred until "a state configuration is reached where these Event types are no longer deferred". This case never happens, therefore the events get deferred again and again. Changing the implementation to make this FSM work would even be a Standard violation., so I am against the change.  
BTW the issue which caused the issue was implemented though I expressed doubts about it, because it was not violating the Standard.

---

## Comment 14

> Username: chandryan  
> Created at: 2025-11-19 20:48:47 UTC  
> Updated at: 2025-11-20 06:13:26 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-3554575234  

> I need to think if I can solve generally, but I think simply conditionally deferring using a guard is a much more stable solution.  
  
That's a fair point and IMO a bearable workaround to achieve UML-compliant behavior as well as circumvent the infinite recursion problem with the older back-ends. You can ensure with guards that the event does not get consumed and deferred at the same time.  
  
Shall we document this limitation, as well as the recommendation to conditionally defer the event?  
  
> BTW the issue which caused the issue was implemented though I expressed doubts about it, because it was not violating the Standard.  
  
While the standard doesn't mandate this behavior, I appreciate the decision that the implementation was changed in this manner - even though it unfortunately led to this issue. Processing deferred events in the same order they've been deferred in without intermittent re-ordering reflects the most "intuitive" behavior to me.  
  
---  
  
In the meantime I was able to draft an implementation in `backmp11`, which behaves UML-compliant in orthogonal regions and also keeps the "intuitive" processing order of deferred events. As an interesting and surprising side effect, this change actually simplified the logic and even led to a noticeable compile time & memory reduction of almost 10% 🥳   
I will try to finalize the implementation work and get it into the upcoming Boost 1.90 release.  
  
Deferring events as an action in a transition is still an unsolved point, though. This is not foreseen in UML, and IMO the possibility of deferring an event as an action triggered by that same event carries an inherent logic error: It defines this event to be consumed *and* deferred at the same time. Thus I'm reluctant to support this at all in `backmp11`.  
  
I think it's too late to remove this feature within `backmp11` for the upcoming release, and I'm not aware of potential needs of it.  
The only use case that comes to my mind is conditional deferral. This *could* possibly be supported in other ways that would not violate the standard, for example by providing an additional method within a state (that receives the event and possibly other arguments), which can be defined by the user and then evaluated at runtime additionally to the `deferred_events` property. This evaluation could then take place before the event is dispatched.  
  
I would like to mark it as deprecated for `backmp11` in this release and create another issue to collect potential needs to have such an additional method available. Then there is a grace period for either replacing it with an alternative mechanism or removing it completely.

---

## Comment 15

> Username: henry-ch  
> Created at: 2025-11-29 17:41:02 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-3591828192  

Please don't! Conditional event deferring is one of the feature which was popular when MSM came out. I personally use it quite often. And in that case, one will need to defer as an action, which is fine because there is a guard.  
I think removing widely used features because one can use it wrong goes against the idea of C++ which, well, will let you blow your leg if you want to ;-)

---

## Comment 16

> Username: chandryan  
> Created at: 2025-12-05 05:01:52 UTC  
> Updated at: 2025-12-05 05:09:12 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-3615317983  

The following changes have been merged to master and are going to be available in Boost 1.90:  
  
- backmp11: Changed the deferral logic with the `deferred_events` property to behave like it's described in UML (https://www.boost.org/doc/libs/master/doc/antora/msm/tutorial/backmp11-back-end.html#_deferring_events_in_orthogonal_regions)  
- back & back11: Documented the limitation related to orthogonal regions and how to work around it (https://www.boost.org/doc/libs/master/doc/antora/msm/tutorial/back-end.html#_deferring_events_in_orthogonal_regions)

---

## Comment 17

> Username: chandryan  
> Created at: 2025-12-19 14:13:06 UTC  
> Url: https://github.com/boostorg/msm/issues/72#issuecomment-3675229478  

Boost 1.90 is released and thus the fix and workaround description are available. Closing this this issue, please create another one if you experience any problems.
