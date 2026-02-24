# #34 - enqueue events on the submachine and execute_enqueue_events on the main machine not compatible [Closed]

> Username: AndrewBloom  
> Created at: 2021-02-25 15:14:45 UTC  
> Updated at: 2024-02-08 16:10:30 UTC  
> Closed at: 2024-02-08 16:10:30 UTC  
> Url: https://github.com/boostorg/msm/issues/34  

To me it seems that execute_enqueue_events should recursively call the same for all the submachines  
  
example:  
```c++  
#include <iostream>  
#include <mutex>  
#include <boost/msm/back/state_machine.hpp>  
#include <boost/msm/front/state_machine_def.hpp>  
#include <boost/msm/front/functor_row.hpp>  
#include <boost/msm/front/euml/common.hpp>  
  
namespace msm = boost::msm;  
namespace mpl = boost::mpl;  
using namespace boost::msm::front;  
  
class SyncBox {  
    //std::mutex mtx;  
    int index;  
};  
  
#define ON_ENTRY template <class Event, class FSM> void on_entry(Event const&, FSM& fsm)  
#define ON_EXIT template <class Event, class FSM> void on_exit(Event const&, FSM& fsm)  
  
struct SubSMFE : public msm::front::state_machine_def<SubSMFE>  
{  
    struct evt1 {};  
    struct evt2 {};  
    struct evt3 {};  
    struct InitState : public msm::front::state<> { ON_ENTRY{ std::cout << "InitState " << std::endl; }; };  
    struct Fake1State : public msm::front::state<> {  
        ON_ENTRY{ std::cout << "Fake1State " << std::endl; };  
    };  
    struct Fake2State : public msm::front::state<> {  
        ON_ENTRY{ std::cout << "Fake2State " << std::endl; };  
    };  
    struct Fake3State : public msm::front::state<> {  
        ON_ENTRY{ std::cout << "Fake3State " << std::endl; };  
    };  
    struct Fake4State : public msm::front::state<> {  
        ON_ENTRY{ std::cout << "Fake4State " << std::endl; };  
    };  
    typedef InitState initial_state;  
    struct transition_table : mpl::vector<  
        Row < InitState, none, Fake1State>,  
        Row < Fake1State, evt1, Fake2State>,  
        Row < Fake2State, evt2, Fake3State>,  
        Row < Fake3State, evt3, Fake4State>  
    > {};  
};  
  
////typedef msm::back::state_machine<SubSMFE> SubSM;  
class SubSM : public msm::back::state_machine<SubSMFE> {  
private:  
    SyncBox* sb;  
public:  
    SubSM() : sb(nullptr) { };  
    SubSM(SyncBox* sb) : sb(sb) { std::cout << "SubSMFE constructor" << std::endl; };  
    void oneFunction() {  
        // here i use syncBox. it must be a function of SubSM, not SubSMFE (oneFunction overrides start or enqueue_event)  
    };  
};  
  
struct mainSMFE : public msm::front::state_machine_def<mainSMFE>  
{  
protected:  
    SyncBox sb;  
public:  
    mainSMFE(int dummy) { };  
    struct InitState : public msm::front::state<> {};  
    typedef InitState initial_state;  
    struct transition_table : mpl::vector<  
        Row < InitState, none, SubSM>  
    > {};  
};  
  
class mainSM : public msm::back::state_machine<mainSMFE> {  
public:  
    mainSM() : msm::back::state_machine<mainSMFE>(msm::back::states_ << SubSM(&sb), 1) { };  
};  
  
int main()  
{  
    mainSM sm;  
    sm.start();  
    auto sub = sm.get_state<SubSM&>();  
    sub.enqueue_event(SubSMFE::evt1());  
    sub.enqueue_event(SubSMFE::evt2());  
    int qsize = sm.get_message_queue_size();  
    int qsubsize = sub.get_message_queue_size();  
    std::cout << "queue size main " << qsize << " sub " << qsubsize << std::endl;  
    std::cout << "--------------------------------" << std::endl;  
    sm.execute_queued_events();  
    sub.enqueue_event(SubSMFE::evt3());  
    sm.execute_queued_events();  
    qsize = sm.get_message_queue_size();  
    qsubsize = sub.get_message_queue_size();  
    std::cout << "queue size main " << qsize << " sub " << qsubsize << std::endl;  
    std::cout << "--------------------------------" << std::endl;  
    sub.execute_queued_events();  
    return 0;  
}  
```  
  
this prints out:   
SubSMFE constructor  
InitState  
Fake1State  
queue size main 0 sub 2  
- - - -----------------------------  
queue size main 0 sub 3  
- - - -----------------------------  
Fake2State  
Fake3State  
Fake4State  
  
  
I would have expected that   sm.execute_queued_events(); would execute the events. This is specially useful cause a submachine can enqueue events with fsm.enqueue_event(my_ev()) on an owned manager class (a networkPacketsManager for example with its own thread).  
Actually, they should have the option to share the same queue, it would make everything much simpler (think synchronisation for example - I managed to use my own queue, and it would be better you used a std::queue instead of std::deque, which has a smaller interface and doesn't have the double action front/pop_front which is problematic if you want to use a lock and a mutex).

---

## Comment 1

> Username: henry-ch  
> Created at: 2024-02-08 16:10:30 UTC  
> Url: https://github.com/boostorg/msm/issues/34#issuecomment-1934456694  

Yes, fsm and submachine use different queues, which is unfortunate in this case. Actually I now think all should happen on the upper fsm, which might be an issue because the sub does not know its upper.  
The new back11 backend state machines now support a _get_upper()_  so that it is now possible.
