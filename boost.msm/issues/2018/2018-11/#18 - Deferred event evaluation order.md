# #18 - Deferred event evaluation order [Closed]

> Username: redboltz  
> Created at: 2018-11-21 04:21:32 UTC  
> Updated at: 2024-02-12 16:23:51 UTC  
> Closed at: 2024-02-12 16:23:51 UTC  
> Url: https://github.com/boostorg/msm/issues/18  

It seems that MSM evaluates deferred events incorrect order.  
  
Please see https://stackoverflow.com/questions/53390674/correct-order-of-deferred-events-in-state-machine-of-uml-2-x  
  
I guess that when `do_handle_deferred()` is called, the event de-queued and en-queued again.  
  
See  
  
`do_handle_deferred()` https://github.com/boostorg/msm/blob/develop/include/boost/msm/back/state_machine.hpp#L1894  
  
`defer_event()`  
https://github.com/boostorg/msm/blob/develop/include/boost/msm/back/state_machine.hpp#L1540  
  
I think that if the event is also deferred in the state during `do_handle_deferred()`, just keep it instead of `pop_front()`.   
  
Here is the code and running demo that reproduces the issue.  
  
https://wandbox.org/permlink/v5hRtdJXRek8RidW  
  
```cpp  
#include <iostream>  
#include <boost/msm/back/state_machine.hpp>  
   
#include <boost/msm/front/state_machine_def.hpp>  
#include <boost/msm/front/functor_row.hpp>  
   
namespace msm = boost::msm;  
namespace msmf = boost::msm::front;  
namespace mpl = boost::mpl;  
  
// ----- Events  
struct Event1 {};  
struct Event2 {};  
struct Event3 {};  
struct Event4 {};  
  
// ----- State machine  
struct Sm1_:msmf::state_machine_def<Sm1_> {  
  
    // States  
    struct State1:msmf::state<> {  
        template <class Event,class Fsm>  
        void on_entry(Event const&, Fsm&) const {  
            std::cout << "State1::on_entry()" << std::endl;  
        }  
    };  
    struct State2:msmf::state<> {  
        template <class Event,class Fsm>  
        void on_entry(Event const&, Fsm&) const {  
            std::cout << "State2::on_entry()" << std::endl;  
        }  
    };  
    struct State3:msmf::state<> {  
        template <class Event,class Fsm>  
        void on_entry(Event const&, Fsm&) const {  
            std::cout << "State3::on_entry()" << std::endl;  
        }  
    };  
    struct State4:msmf::state<> {  
        template <class Event,class Fsm>  
        void on_entry(Event const&, Fsm&) const {  
            std::cout << "State4::on_entry()" << std::endl;  
        }  
    };  
    struct State5:msmf::state<> {  
        template <class Event,class Fsm>  
        void on_entry(Event const&, Fsm&) const {  
            std::cout << "State5::on_entry()" << std::endl;  
        }  
    };  
  
    // Set initial state  
    typedef State1 initial_state;  
    // Enable deferred capability  
    typedef int activate_deferred_events;  
    // Transition table  
    struct transition_table:mpl::vector<  
        //          Start   Event   Next        Action       Guard  
        msmf::Row < State1, Event1, msmf::none, msmf::Defer, msmf::none >,  
        msmf::Row < State1, Event2, msmf::none, msmf::Defer, msmf::none >,  
        msmf::Row < State1, Event3, msmf::none, msmf::Defer, msmf::none >,  
        msmf::Row < State1, Event4, State2,     msmf::none,  msmf::none >,  
  
        msmf::Row < State2, Event1, msmf::none, msmf::Defer, msmf::none >,  
        msmf::Row < State2, Event3, msmf::none, msmf::Defer, msmf::none >,  
        msmf::Row < State2, Event2, State3,     msmf::none,  msmf::none >,  
  
        msmf::Row < State3, Event1, State4,     msmf::none,  msmf::none >,  
        msmf::Row < State3, Event3, State5,     msmf::none,  msmf::none >  
    > {};  
      
    template <class Fsm, class Event>   
    void no_transition(Event const&, Fsm&, int /*state*/) {  
        if constexpr (std::is_same_v<Event, Event1>) {  
            std::cout << "Event1 disposed" << std::endl;  
        }  
        else if constexpr(std::is_same_v<Event, Event2>) {  
            std::cout << "Event2 disposed" << std::endl;  
        }  
        else if constexpr(std::is_same_v<Event, Event3>) {  
            std::cout << "Event3 disposed" << std::endl;  
        }  
        else if constexpr(std::is_same_v<Event, Event4>) {  
            std::cout << "Event4 disposed" << std::endl;  
        }  
    }  
};  
  
// Pick a back-end  
typedef msm::back::state_machine<Sm1_> Sm1;  
   
int main() {  
    Sm1 sm1;  
    sm1.start();   
    std::cout << "> Send Event1" << std::endl;  
    sm1.process_event(Event1());  
    std::cout << "> Send Event2" << std::endl;  
    sm1.process_event(Event2());  
    std::cout << "> Send Event3" << std::endl;  
    sm1.process_event(Event3());  
    std::cout << "> Send Event4" << std::endl;  
    sm1.process_event(Event4());  
}  
```

---

## Comment 1

> Username: henry-ch  
> Created at: 2024-02-12 16:23:51 UTC  
> Url: https://github.com/boostorg/msm/issues/18#issuecomment-1939074236  

Frankly, I'm unconvinced that the UML Standard is mandating an order in the deferred events.  
Not processing the deferred event from the pool is not an option: you could have a guard, and the result of which is only known at run-time.  
I still fixed a bug which was preventing Event1 to be processed anyway and added a sorting to try and keep the original order, so that your test case now works.
