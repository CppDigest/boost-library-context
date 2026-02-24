# #47 - Stuck in pseudo exit state problem [Closed]

> Username: galois-advertising  
> Created at: 2023-04-28 06:52:23 UTC  
> Updated at: 2024-02-09 07:28:09 UTC  
> Closed at: 2024-02-09 07:28:08 UTC  
> Url: https://github.com/boostorg/msm/issues/47  

I found a someone encounter the same problem here   
[Google Groups: Stuck in pseudo exit](https://groups.google.com/g/boost-list/c/J_Re9iy7H58/m/aWXbSE6vUnUJ)  
But he does not give the completely code.  
The following code will show the bug, use another thread to process_event(ev_on_timestamp()) will trigger the bug.  
# FSM definition  
![msm_pseudo_exit_bug](https://user-images.githubusercontent.com/50731495/235071699-4b2ce2dd-7b2c-43d0-b8e5-9e9d47faebe0.png)  
# MSM implement  
```c++  
#define BOOST_TEST_MODULE test_strategies_strategy_common_test  
#include <boost/test/unit_test.hpp>  
#include <assert.h>  
#include <iostream>  
#include <thread>  
#include <chrono>  
#include <boost/msm/back/state_machine.hpp>  
#include <boost/msm/front/state_machine_def.hpp>  
#include <boost/msm/front/functor_row.hpp>  
#include <boost/msm/front/euml/operator.hpp>  
  
namespace msm = boost::msm;  
namespace mpl = boost::mpl;  
using namespace msm::front;  
using namespace msm::front::euml;  
std::thread td;  
bool is_buggy = false;  
bool hit_no_transition = false;  
  
struct ev_setup {};  
  
struct start : public msm::front::state<> {  
  const char *name() { return "start"; }  
  template <class Event, class FSM>  
  void on_entry(Event const &event, FSM &fsm) {  
    std::cout << name() << ":" << __FUNCTION__ << std::endl;  
  }  
  template <class Event, class FSM>  
  void on_exit(Event const &event,FSM &fsm) {  
    std::cout << name() << ":" << __FUNCTION__ << std::endl;  
  }  
};  
  
struct end : public msm::front::state<> {  
  const char *name() { return "end"; }  
  template <class Event, class FSM>  
  void on_entry(Event const &event, FSM &fsm) {  
    std::cout << name() << ":" << __FUNCTION__ << std::endl;  
  }  
  template <class Event, class FSM>  
  void on_exit(Event const &event,FSM &fsm) {  
    std::cout << name() << ":" << __FUNCTION__ << std::endl;  
  }  
};  
  
struct sub_machine : public msm::front::state_machine_def<sub_machine> {  
  struct ev_on_timestamp {};  
  
  struct ev_on_result {};  
  
  struct sub_start : public msm::front::state<> {  
    const char *name() { return "sub_start"; }  
    template <class Event, class FSM>  
    void on_entry(Event const &event, FSM &fsm) {  
      // sub_start send a `ev_on_timestamp` within `on_entry` to trigger the transition to `sub_processing`  
      std::cout << name() << ":" << __FUNCTION__ << std::endl;  
      if (is_buggy) {  
        // send `ev_on_timestamp` in another thread after 3 seconds will lead to 'no transition'  
        // and stuck in pseudo exit state  
        td = std::thread([&fsm]() {  
          std::this_thread::sleep_for(std::chrono::seconds(3));  
          fsm.process_event(ev_on_timestamp());  
          std::cout << "ev_on_timestamp sent" << std::endl;  
        });  
      } else {  
        // send `ev_on_timestamp` in this thread right now, works as expected.  
        fsm.process_event(ev_on_timestamp());  
      }  
    }  
    template <class Event, class FSM>  
    void on_exit(Event const &event,FSM &fsm) {  
      std::cout << name() << ":" << __FUNCTION__ << std::endl;  
    }  
  };  
  
  struct sub_processing : public msm::front::state<> {  
    const char *name() { return "sub_processing"; }  
    template <class Event, class FSM>  
    void on_entry(Event const &event, FSM &fsm) {  
      std::cout << name() << ":" << __FUNCTION__ << std::endl;  
      fsm.process_event(ev_on_result());  
    }  
    template <class Event, class FSM>  
    void on_exit(Event const &event,FSM &fsm) {  
      std::cout << name() << ":" << __FUNCTION__ << std::endl;  
    }  
  };  
  
  struct sub_exit : public msm::front::exit_pseudo_state<ev_on_result> {  
    const char *name() { return "sub_exit"; }  
    template <class Event, class FSM>  
    void on_entry(Event const &event, FSM &fsm) {  
      std::cout << name() << ":" << __FUNCTION__ << std::endl;  
    }  
    template <class Event, class FSM>  
    void on_exit(Event const &event,FSM &fsm) {  
      std::cout << name() << ":" << __FUNCTION__ << std::endl;  
    }  
  };  
  
  typedef sub_start initial_state;  
  
  template <class Event, class FSM>  
  void on_entry(Event const &event, FSM &fsm) {  
    std::cout << "sub_machine:on_entry" << std::endl;  
  }  
  template <class Event, class FSM>  
  void on_exit(Event const &event, FSM &fsm) {  
    std::cout << "sub_machine:exit" << std::endl;  
  }  
  
  template <class Fsm,class Event>  
  void no_transition(Event const& e, Fsm& fsm,int state) {  
    if constexpr(std::is_same<Event, ev_on_result>::value) {  
      // buggy code will reaches here  
      std::cout << "no_transition: sub_machine " << state << std::endl;  
      hit_no_transition = true;  
    }  
  }  
  
  struct transition_table : mpl::vector<  
  //   Start          Event              Next              Action  Guard  
  //   +--------------+------------------+-----------------+-------+----  
  Row< sub_start      , ev_on_timestamp  , sub_processing  , none  , none >,  
  Row< sub_processing , ev_on_result     , sub_exit        , none  , none >  
  > {};  
};  
typedef msm::back::state_machine<sub_machine> sub_fsm;  
  
struct machine : public msm::front::state_machine_def<machine> {  
  typedef start initial_state;  
  template <class Event, class FSM>  
  void on_entry(Event const &event, FSM &fsm) {  
    std::cout << "machine:on_entry" << std::endl;  
  }  
  template <class Event, class FSM>  
  void on_exit(Event const &event, FSM &fsm) {  
    std::cout << "machine:exit" << std::endl;  
  }  
  
  template <class Fsm,class Event>  
  void no_transition(Event const& e, Fsm& fsm,int state) {  
    std::cout << "no_transition: machine " << state << std::endl;  
  }  
  
  struct transition_table : mpl::vector<  
      //   Start                                Event                           Next         Action  Guard  
      //   +------------------------------------+-------------------------------+---------  -+-------+----  
      Row< start                              , ev_setup                      , sub_fsm    , none  , none >,  
      Row< sub_fsm::exit_pt<sub_fsm::sub_exit>, sub_machine::ev_on_result     , end        , none  , none >  
  > {};  
};  
typedef msm::back::state_machine<machine> fsm;  
  
BOOST_AUTO_TEST_CASE(test_pseudo_exit_buggy) {  
  is_buggy = true;  
  hit_no_transition = false;  
  fsm m;  
  m.start();  
  m.process_event(ev_setup());  
  std::this_thread::sleep_for(std::chrono::seconds(5));  
  BOOST_TEST(hit_no_transition == true);  
}  
  
BOOST_AUTO_TEST_CASE(test_pseudo_exit_ok) {  
  is_buggy = false;  
  hit_no_transition = false;  
  fsm m;  
  m.start();  
  m.process_event(ev_setup());  
  std::this_thread::sleep_for(std::chrono::seconds(5));  
  BOOST_TEST(hit_no_transition == false);  
}  
```  
The buggy test case output   
```  
machine:on_entry  
start:on_entry  
start:on_exit  
sub_machine:on_entry  
sub_start:on_entry  
sub_start:on_exit  
sub_processing:on_entry  
sub_processing:on_exit  
sub_exit:on_entry <------------stuck here, the `ev_on_result` is not forwarded to outer fsm  
no_transition: sub_machine 2  
ev_on_timestamp sent  
```  
  
The ok test case output  
```  
start:on_entry  
start:on_exit  
sub_machine:on_entry  
sub_start:on_entry  
sub_start:on_exit  
sub_processing:on_entry  
sub_processing:on_exit  
sub_exit:on_entry  
sub_exit:on_exit  
sub_machine:exit  
end:on_entry  
```

---

## Comment 1

> Username: henry-ch  
> Created at: 2024-02-09 07:28:08 UTC  
> Url: https://github.com/boostorg/msm/issues/47#issuecomment-1935450854  

Sorry, msm is not thread-safe, so there is no guarantee this would work. I suggest making the whole state machine execute in an executor of if you excuse self-advertising: https://github.com/henry-ch/asynchronous
