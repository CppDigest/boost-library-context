# #81 - back11: undefined behaviour (reported by llvm 18) [Open]

> Username: matthijs  
> Created at: 2024-12-05 10:28:22 UTC  
> Updated at: 2025-03-21 15:46:39 UTC  
> Url: https://github.com/boostorg/msm/issues/81  

Hi,  
  
Compiler: clang++-18  
Boost: version 1.86.0  
  
I have the following statemachine:  
```c++  
#include <iostream>  
  
#include <boost/msm/back11/state_machine.hpp>  
#include <boost/msm/front/puml/puml.hpp>  
#include <boost/msm/front/state_machine_def.hpp>  
  
namespace msm = boost::msm;  
using namespace msm::front;  
using namespace msm::front::puml;  
  
namespace boost::msm::front::puml {  
  
// Actions  
template <> struct Action<by_name("a_connected")> {  
  template <typename Event, typename FSM, typename SourceState,  
            typename TargetState>  
  void operator()(const Event &, FSM &, SourceState &, TargetState &) {  
    std::cout << "server connected" << std::endl;  
  }  
};  
} // namespace boost::msm::front::puml  
  
struct server_ : public msm::front::state_machine_def<server_> {  
  int chain_id{0};  
  int job_id{0};  
  BOOST_MSM_PUML_DECLARE_TABLE(R"(  
@startuml server  
state Server {  
[*]       --> Initial  
Initial   --> Connected : connected / a_connected  
}  
@enduml  
)")  
};  
using server = msm::back11::state_machine<server_>;  
using connected = Event<by_name("connected")>;  
  
int main(int, char **) {  
  
  // server  
  server sm;  
  sm.process_event(connected{});  
  
  return 0;  
}  
```  
  
When compiling and executing:  
```c++  
$ /usr/bin/clang++-18 -isystem /home/matthijs/src/boost/boost_1_86_0 -fno-omit-frame-pointer -O1 -fsanitize=address,undefined,pointer-compare,pointer-subtract,leak -fsanitize-address-use-after-scope -Wall -Wextra -g -std=gnu++20 ../main.cpp -o main  
$ ./main  
/home/matthijs/src/boost/boost_1_86_0/boost/msm/back11/state_machine.hpp:2171:21: runtime error: call to function boost::msm::back11::state_machine<server_, void, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>::a_row_<boost::msm::front::Row<boost::msm::front::puml::State<2389040076u, boost::fusion::vector<>, boost::fusion::vector<>, boost::fusion::vector<>>, boost::msm::front::puml::Event<3846760266u>, boost::msm::front::puml::State<1740203497u, boost::fusion::vector<>, boost::fusion::vector<>, boost::fusion::vector<>>, boost::msm::front::puml::Action<2978207285u>, boost::msm::front::none>>::execute(boost::msm::back11::state_machine<server_, void, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>&, int, int, boost::msm::front::puml::Event<3846760266u> const&) through pointer to incorrect function type 'boost::msm::back::HandledEnum (*)(boost::msm::back11::state_machine<server_> &, int, int, boost::msm::front::puml::Event<3846760266> &)'  
/home/matthijs/src/boost/boost_1_86_0/boost/msm/back11/state_machine.hpp:590: note: boost::msm::back11::state_machine<server_, void, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>::a_row_<boost::msm::front::Row<boost::msm::front::puml::State<2389040076u, boost::fusion::vector<>, boost::fusion::vector<>, boost::fusion::vector<>>, boost::msm::front::puml::Event<3846760266u>, boost::msm::front::puml::State<1740203497u, boost::fusion::vector<>, boost::fusion::vector<>, boost::fusion::vector<>>, boost::msm::front::puml::Action<2978207285u>, boost::msm::front::none>>::execute(boost::msm::back11::state_machine<server_, void, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>&, int, int, boost::msm::front::puml::Event<3846760266u> const&) defined here  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /home/matthijs/src/boost/boost_1_86_0/boost/msm/back11/state_machine.hpp:2171:21  
server connected  
```  
  
I am not sure if this is a serious problem. If you need more information let me know.  
  
Regards, Matthijs

---

## Comment 1

> Username: PiotrNycz  
> Created at: 2025-03-21 15:46:37 UTC  
> Url: https://github.com/boostorg/msm/issues/81#issuecomment-2743770126  

I have the same problem with "back" -- not "back11" - with clang UB-sanitizer starting from version 1.86 - I do not know when it started - my previous version of boost was 1.78 (it worked there):  
  
> runtime error: call to function boost::msm::back::dispatch_table<boost::msm::back::state_machine<.*>>::execute(.*) through pointer to incorrect function type.*  
   
  
AFAICS - the incorrectness of function --> function pointers is due the fact that pointer to function expects "const Event&" while this execute function expects "Event&" (not const). But I am not 100% I did not mistake in this. I just add it to suppression list and "forgot"
