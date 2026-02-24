# #77 - new back11::state_machine will skip certain transitions [Closed]

> Username: auzn88  
> Created at: 2024-08-22 12:13:45 UTC  
> Updated at: 2024-09-02 16:58:55 UTC  
> Closed at: 2024-09-02 16:57:42 UTC  
> Url: https://github.com/boostorg/msm/issues/77  

For a state machine like this:  
  
```c++  
#include <iostream>  
  
// #define USE_PRE11_MSM_BACKEND   // <- toggle this to switch between back vs back11  
  
// back-end  
#if defined(USE_PRE11_MSM_BACKEND)  
#include <boost/msm/back/state_machine.hpp>  
namespace msm_back = boost::msm::back;  
#else  
#include <boost/msm/back11/state_machine.hpp>  
namespace msm_back = boost::msm::back11;  
#endif  
  
// front-end  
#include <boost/fusion/include/insert_range.hpp>  
#include <boost/msm/front/euml/operator.hpp>  
#include <boost/msm/front/functor_row.hpp>  
#include <boost/msm/front/state_machine_def.hpp>  
  
  
template <typename ...T>  
using msm_flags = boost::mpl::vector<T...>;  
template <typename ...T>  
using msm_transition_table = boost::mpl::vector<T...>;  
  
  
  
namespace msmf = boost::msm::front;  
namespace euml = boost::msm::front::euml;  
  
struct evt1{};  
struct evt2{};  
struct evt3{};  
  
struct top_ : public msmf::state_machine_def<top_> {  
    template <class Event,class FSM>  
    void on_entry(Event const& ,FSM&) {std::cout << "entering: top" << std::endl;}  
  
    struct nested : public msmf::state<> {  
        template <class Event,class FSM>  
        void on_entry(Event const& ,FSM&) {std::cout << "entering: nested" << std::endl;}  
    };  
  
    struct other1 : public msmf::state<> {  
        template <class Event,class FSM>  
        void on_entry(Event const& ,FSM&) {std::cout << "entering: other1" << std::endl;}  
    };  
  
    struct other2 : public msmf::state<> {  
        template <class Event,class FSM>  
        void on_entry(Event const& ,FSM&) {std::cout << "entering: other2" << std::endl;}  
    };  
  
    struct other3 : public msmf::state<> {  
        template <class Event,class FSM>  
        void on_entry(Event const& ,FSM&) {std::cout << "entering: other3" << std::endl;}  
    };  
  
    struct other4 : public msmf::state<> {  
        template <class Event,class FSM>  
        void on_entry(Event const& ,FSM&) {std::cout << "entering: other4" << std::endl;}  
    };  
  
    struct guard_true {  
        template <class EVT, class FSM, class SourceState, class TargetState>  
        bool operator()(EVT const&, FSM& fsm, SourceState&, TargetState&) const {  
            return true;  
        }  
    };  
  
    struct guard_false {  
        template <class EVT, class FSM, class SourceState, class TargetState>  
        bool operator()(EVT const&, FSM& fsm, SourceState&, TargetState&) const {  
            return false;  
        }  
    };  
  
      
    typedef nested initial_state;  
      
    using transition_table = msm_transition_table<  
        //          Start        Event     Next         Action      Guard  
        //--------+------------+---------+------------+-----------+--------------  
        msmf::Row < nested , msmf::none    , other1  , msmf::none, guard_false>,  
        msmf::Row < nested , msmf::none    , other2  , msmf::none, guard_true >,  // <- this transition is not executed with back11  
        msmf::Row < nested , msmf::none    , other3  , msmf::none, guard_false>,  
        msmf::Row < nested , msmf::none    , other4  , msmf::none, guard_false>,  
        msmf::Row < other1 , evt3          , nested  , msmf::none, msmf::none       >  
    >;  
};  
  
using top = msm_back::state_machine<top_>;  
  
int main(){  
    top msm;  
    msm.start();  
}  
```   
  
Godbolt link: https://godbolt.org/z/Pcr6h5vPa  
  
Using back11 vs back will have different result. If using back, the state will reach `other2`, but if using back11, the state will stuck at `nested`.   
  
Also, using back11, if setting guard to `other1` or `other4` to be `true`, the transition will be executed. Only transition to `other2` and `other3` has issues.

---

## Comment 1

> Username: auzn88  
> Created at: 2024-08-23 02:48:47 UTC  
> Url: https://github.com/boostorg/msm/issues/77#issuecomment-2306083955  

I should mention I am using boost 1.85.0

---

## Comment 2

> Username: henry-ch  
> Created at: 2024-09-02 16:58:54 UTC  
> Url: https://github.com/boostorg/msm/issues/77#issuecomment-2325105072  

Thank you for providing a test case! Fixed, will be merged for 1.87
