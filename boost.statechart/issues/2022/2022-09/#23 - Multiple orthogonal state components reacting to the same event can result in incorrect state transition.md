# #23 - Multiple orthogonal state components reacting to the same event can result in incorrect state transition [Open]

> Username: mkatliar  
> Created at: 2022-09-08 13:21:55 UTC  
> Updated at: 2023-02-02 11:41:22 UTC  
> Url: https://github.com/boostorg/statechart/issues/23  

Consider an example state machine:  
![orthogonal_regions](https://user-images.githubusercontent.com/8616371/189132048-25792a3f-4bb5-4169-a132-82f879cca68f.png)  
  
```c++  
#include <boost/statechart/state_machine.hpp>  
#include <boost/statechart/simple_state.hpp>  
#include <boost/statechart/transition.hpp>  
#include <boost/mpl/list.hpp>  
   
#include <iostream>  
   
struct Main;  
struct A0;  
struct B0;  
struct A1;  
struct B1;  
   
struct Event : boost::statechart::event<Event> {};  
struct Machine : boost::statechart::state_machine<Machine, Main> {};  
struct Main : boost::statechart::simple_state<Main, Machine, boost::mpl::list<A0, A1>> {};  
   
   
struct A0 : boost::statechart::simple_state<A0, Main::orthogonal<0>>  
{  
   using reactions = boost::statechart::transition<Event, B0>;  
   A0() { std::cout << "Enter A0" << std::endl; }  
   ~A0() { std::cout << "Exit A0" << std::endl; }  
};  
   
struct B0 : boost::statechart::simple_state<B0, Main::orthogonal<0>>  
{  
   B0() { std::cout << "Enter B0" << std::endl; }  
   ~B0() { std::cout << "Exit B0" << std::endl; }  
};  
   
struct A1 : boost::statechart::simple_state<A1, Main::orthogonal<1>>  
{  
   using reactions = boost::statechart::transition<Event, B1>;  
   A1() { std::cout << "Enter A1" << std::endl; }  
   ~A1() { std::cout << "Exit A1" << std::endl; }  
};  
   
struct B1 : boost::statechart::simple_state<B1, Main::orthogonal<1>>  
{  
   B1() { std::cout << "Enter B1" << std::endl; }  
   ~B1() { std::cout << "Exit B1" << std::endl; }  
};  
   
   
int main(int, char**)  
{  
   Machine machine;  
   machine.initiate();  
   machine.process_event(Event {});  
   
   return 0;  
}  
```  
  
Running the example outputs:  
```  
Enter A0  
Enter A1  
Exit A0  
Enter B0  
Exit A1  
Exit B0  
```  
  
The machine transitions (A0, A1)->(B0, A1), although the correct transition is (A0, A1)->(B0, B1).

---

## Comment 1

> Username: jrutgeer  
> Created at: 2023-02-02 11:41:22 UTC  
> Url: https://github.com/boostorg/statechart/issues/23#issuecomment-1413603104  

@mkatliar This is mentioned as a limitation: see the [Boost Statechart Rationale](https://www.boost.org/doc/libs/1_53_0/libs/statechart/doc/rationale.html#Limitations), scroll down to paragraph **Event dispatch to orthogonal regions**.  
It shows a simple workaround.
