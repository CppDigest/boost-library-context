# #6 Bugfix: Deferred and queued event iterative dispatch [Merged]

> Username: MrTam  
> Created at: 2015-09-03 09:47:20 UTC  
> Updated at: 2015-09-16 10:29:07 UTC  
> Merged at: 2015-09-11 13:25:43 UTC  
> Closed at: 2015-09-11 13:25:43 UTC  
> Url: https://github.com/boostorg/msm/pull/6  

Currently, MSM dispatches events that have been placed on the deferred queue and message queue via a recursive call mechanism, where enqueued events are processed in the same run-to-completion algorithm as the one that called them.  
  
Whilst this works under the majority of cases, large numbers of events being enqueued cause the stack to be blown when processing from the queue.  
  
The proposed fix for this is within this pull request. The general idea of this fix is:  
- Process enqueued events iteratively in a while loop, rather than using recursion.  
- Using a 'sequence' identifier within the deferred queue. Each successful handle of an event / transition causes the sequence to be updated. When the sequence associated with an enqueued event is past the current sequence, we yield and allow processing to resume from the calling `process_event_internal()`. This ensures events deferred aren't processed in the same RTC cycle.  
- No longer iterate the entire deferred queue to mark events to be handled, due to the new sequence mechanism.  
- Only processing the deferred queue and message queue if we aren't currently handling a deferred event. This ensures recursion doesn't take place, but still guarantees event dispatch ordering compatible with the latest version of the UML.  
- Change the signature of `process_event_internal` from `Event, bool` to `Event, EventSource`. `EventSource` is a bitset declared in `back/common_types.hpp` which allows us to define if we are processing an event from the message queue, deferred queue, and if we are in a direct call.  
- Some small optimisation fixes to reduce the numbers of copies, tidy up line endings etc.  
  
I have re-run the MSM unit tests, and these all still pass successfully:  
  
```  
./CompositeMachine.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./SimpleEuml2.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./Entries.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./OrthogonalDeferred2.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./OrthogonalDeferred.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./SimpleEuml.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./SerializeWithHistory.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./Serialize.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./SerializeSimpleEuml.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./AnonymousEuml.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./Constructor.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./SimpleInternal.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./EventQueue.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./CompositeEuml.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./TestConstructor.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./OrthogonalDeferred3.cpp.bin  
Running 1 test case...  
play 0  
is_play: 1  
MyDefer  
play 1  
play 1b  
is_play: 0  
play 1c  
is_play: 1  
MyDefer  
play 1d  
is_play: 0  
play 2  
play 3  
  
*** No errors detected  
./History.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./SimpleMachine.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./SimpleWithFunctors.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./OrthogonalDeferredEuml.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./SimpleInternalEuml.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./SimpleInternalFunctors.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
./Anonymous.cpp.bin  
Running 1 test case...  
  
*** No errors detected  
```  
  
Consideration has been given to ensure the UML is honoured, as well as preservation of existing behaviour, including orthogonal region processing, submachines, and flags.  
  
Any further questions on this, I'd be happy to answer, and / or modify the code to suit.  
  
Best Regards,  
Tom

---

## Comment 1

> Username: lskillen  
> Created_at: 2015-09-07 12:21:19 UTC  
> Url: https://github.com/boostorg/msm/pull/6#issuecomment-138282420  

Hey Christophe,  
  
I'm helping out Thomas on this one - He's currently away on holiday (probably trying to not think about state machines for awhile).  I thought I'd add some additional details for the test results that we seen in standard MSM versus the patched (feature-iterative) variant of MSM.  These are performance oriented but also demonstrate the points at which cores occur due to the memory growing beyond the maximum stack size (as detailed previously).  
  
Just to note that although performance is a key concern, correctness and avoiding the stack issues are the most important for us.  We're certain that our patch as is isn't perfect, that we're likely breaking some compatibility, and there may be additional issues with the iterative approach, but it's worth exploring anyway.  If you feel like the core approach of the change is acceptable but there are aspects of the patch that you dislike we're happy to make any changes required.  
  
The comparison setup uses a variant of the batching example that Thomas sent to you privately via email (we can make it and the test scripts available here if desirable) - We compile two optimised binaries, one with the standard MSM and another with the feature-iterative MSM as such:  
  
```  
g++ -o batcher_standard -std=c++11 -g2 -O3 -Wall -Wextra -DNDEBUG=1 -D_NDEBUG=1 -I. Main.cc  
g++ -o batcher_feature_iterative -std=c++11 -g2 -O3 -Wall -Wextra -DNDEBUG=1 -D_NDEBUG=1 -I. -I../include Main.cc  
```  
  
There's a test script to automate the calls, but it isn't any more complicated than setting up a number of parametrised calls to the binaries - The result of this is the following output:  
  
```  
Running batcher_standard (event processing in standard MSM) ...  
  
  BATCH   TOTAL CORED REAL      SYS     USER  
     10     100    NO 0:00.00   0.00    0.00  
    100    1000    NO 0:00.00   0.00    0.00  
    500    1000    NO 0:00.00   0.00    0.00  
   1000   10000    NO 0:00.01   0.01    0.00  
   5000   10000    NO 0:00.07   0.06    0.00  
  10000  100000   YES 0:00.34   0.24    0.00  
  20000  100000   YES 0:01.19   1.08    0.01  
  40000  100000   YES 0:03.93   3.83    0.00  
  80000  100000   YES 0:14.52   14.40   0.00  
 100000 1000000   YES 0:22.62   22.48   0.01  
  
Running batcher_feature_iterative (event processing in feature-iterative MSM) ...  
  
  BATCH   TOTAL CORED REAL      SYS     USER  
     10     100    NO 0:00.00   0.00    0.00  
    100    1000    NO 0:00.00   0.00    0.00  
    500    1000    NO 0:00.00   0.00    0.00  
   1000   10000    NO 0:00.00   0.00    0.00  
   5000   10000    NO 0:00.00   0.00    0.00  
  10000  100000    NO 0:00.01   0.00    0.00  
  20000  100000    NO 0:00.01   0.00    0.00  
  40000  100000    NO 0:00.01   0.00    0.00  
  80000  100000    NO 0:00.01   0.00    0.00  
 100000 1000000    NO 0:00.09   0.09    0.00  
```  
  
So we can see that the iterative variant is faster and doesn't exceed the maximum stack size - In the standard code we can see that the length of time to process the message queues doesn't grow linearly with the queue.  The patched code seems to resolve this although we haven't performed exhaustive tests as to the correctness of the code, especially with more advanced MSM features, other than building the standard MSM tests, running this batcher example and similar code.    
  
One other thing that I noticed was the following minor warnings when compiling under NDEBUG (seen because of -Wextra/-Wunused-parameter):  
  
```  
boost/msm/back/state_machine.hpp:521:75: warning: unused parameter ‘state’ [-Wunused-parameter]  
         static HandledEnum execute(library_sm& fsm, int region_index, int state, transition_event const& evt)  
boost/msm/back/state_machine.hpp:704:40: warning: unused variable ‘current_state’ [-Wunused-variable]  
             BOOST_STATIC_CONSTANT(int, current_state = (get_state_id<stt,current_state_type>::type::value));  
boost/msm/back/state_machine.hpp:701:63: warning: unused parameter ‘state’ [-Wunused-parameter]  
         static HandledEnum execute(library_sm& fsm, int , int state, transition_event const& evt)  
boost/msm/back/state_machine.hpp:650:75: warning: unused parameter ‘state’ [-Wunused-parameter]  
         static HandledEnum execute(library_sm& fsm, int region_index, int state, transition_event const& evt)  
```  
  
They're mostly innocuous because the usage of state/current_state is removed by the preprocessor for the BOOST_ASSERT macros.  Not sure of a more Boost-like way to remove them other than adding a no-op on state/current_state near the macros, such as:  
  
```  
#define _unused(x) ((void)x)  
_unused(state);  
_unused(current_state);  
```  
  
Not important for the discussion of this pull request though!  
  
Looking forward to any thoughts you had about the pull request - We really appreciate your input, and thank you for already responding to Thomas via emails.  I know he said you had concerns about the iterative approach affecting other functionality (so do we).  Hopefully we can work something out.  
  
Thanks,  
Lee

---

## Comment 2

> Username: henry-ch  
> Created_at: 2015-09-11 13:35:58 UTC  
> Url: https://github.com/boostorg/msm/pull/6#issuecomment-139547759  

Hi Lee, hi Tom,  
  
I think this is a very nice solution and great implementation! Very nice!  
To be honest, I'm also unsure that we are not breaking anything but there are so many combinations that it's very hard to tell. I added a new test case for what I thought could break (deferred event and 2 regions, one processing the event and one deferring it). I suppose if it has to break, it would be in such a case, and it does not. It does react surprisingly (one region processes, the other one defers so the event always stays in the queue) but it is what MSM was already doing, so no regression (whether it is UML - conform clearly is disputable but I think it is).  
I think I can merge it with good feeling and I will add a note of caution in the release notes, just in case.  
  
If you don't mind, I will add both of you in the acknowledgement section.  
  
Sorry that this problem bit you. Actually it had bit me too just before my vacations but I had no more time to fix it before going.  
  
Thanks,  
Christophe

---

## Comment 3

> Username: lskillen  
> Created_at: 2015-09-11 15:00:15 UTC  
> Url: https://github.com/boostorg/msm/pull/6#issuecomment-139568719  

Much appreciated Christophe, and we're absolutely glad to help out and contribute.  Boost MSM is a fantastic library that we see a lot of use of here, and we're likely to make future contributions - We're particularly interested in adaptations towards C++11 (and beyond now), which you've probably seen from some of Thomas' pre-PR changes (that we removed to maintain compatibility!)  Cheers!

---

## Comment 4

> Username: henry-ch  
> Created_at: 2015-09-14 19:23:04 UTC  
> Url: https://github.com/boostorg/msm/pull/6#issuecomment-140182196  

Thanks! If you want to provide a patch for the same bug using C++11, it is welcome.  
I have for this a msm3 branch, which will require C++11. I don't have much time to work on it at the moment, as I want to get (Boost) asynchronous ready for review, but I started a few things (a lockfree version and a new front-end mostly).  
Cheers!

---
