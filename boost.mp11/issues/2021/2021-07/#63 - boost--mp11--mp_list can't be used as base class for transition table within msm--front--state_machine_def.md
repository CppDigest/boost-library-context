# #63 - boost::mp11::mp_list can't be used as base class for transition table within msm::front::state_machine_def [Open]

> Username: PiotrNycz  
> Created at: 2021-07-01 07:10:49 UTC  
> Updated at: 2022-01-10 14:22:11 UTC  
> Url: https://github.com/boostorg/mp11/issues/63  

I just wanted to modernize my code - and try to use `boost::mp11::mp_list` instead of `boost::mpl::vector` in my FSM definitions.  
  
Simplified code is as follows:  
  
```  
#include <iostream>  
#include <boost/msm/back/state_machine.hpp>  
#include <boost/msm/front/state_machine_def.hpp>  
  
#include <boost/mp11/list.hpp>  
#include <boost/mp11/algorithm.hpp>  
#include <boost/mp11/mpl.hpp>  
  
namespace msm = boost::msm;  
  
namespace test_fsm   
{  
struct play {};  
struct stop {};  
struct player_ : public msm::front::state_machine_def<player_>  
{  
    typedef int no_exception_thrown;  
    typedef int no_message_queue;  
  
    struct Empty : public msm::front::state<>   
    {  
        template <class Event,class FSM>   void on_entry(Event const&,FSM& ) { std::cout << "entering: Empty" << std::endl; }  
        template <class Event,class FSM>   void on_exit(Event const&,FSM& ) { std::cout << "leaving: Empty" << std::endl; }  
    };  
    struct Playing : public msm::front::state<>  
    {  
        template <class Event,class FSM>    void on_entry(Event const&,FSM& ) { std::cout << "entering: Playing" << std::endl; }  
        template <class Event,class FSM>    void on_exit(Event const&,FSM& ) { std::cout << "leaving: Playing" << std::endl; }  
    };  
  
    typedef Empty initial_state;  
  
    // transition actions  
    void playing(play const&)  {  }  
    void stop_playing(stop const&)  {  }  
      
    typedef player_ p;   
  
    struct transition_table : boost::mp11::mp_list<  
            _row < Empty , play        , Playing       >,  
            _row < Playing , stop        , Empty       >  
    > {};  
  
    // Replaces the default no-transition response.  
    template <class FSM,class Event>  
    void no_transition(Event const& e, FSM&,int state)  
    {  
        std::cout << "no transition from state " << state  
            << " on event " << typeid(e).name() << std::endl;  
    }  
  
    typedef msm::back::state_machine<player_> player;  
  
    //  
    // Testing utilities.  
   //  
   static char const* const state_names[] = { "Empty", "Playing" };  
  
    void pstate(player const& p)  
   {  
       std::cout << " -> " << state_names[p.current_state()[0]] << std::endl;  
   }  
};  
  
}  
  
int main()  
{  
    test_fsm::player p2;  
    p2.start();  
    p2.process_event(test_fsm::play());  
    p2.process_event(test_fsm::stop());   
    return 0;  
}  
  
```  
  
It compiles, but produces unexpected output:  
  
```  
entering: Empty  
no transition from state 0 on event N8test_fsm4playE  
no transition from state 0 on event N8test_fsm4stopE  
  
```  
  
While - with original version (just replacing mp11::mp_list with mpl::vector) it works as expected:  
  
```  
entering: Empty  
leaving: Empty  
entering: Playing  
leaving: Playing  
entering: Empty  
  
```  
  
It also works as expected with `boost::fusion::vector`.  
  
I found one workaround - when using type-alias for defining transition table it works as expected:  
  
```  
    using transition_table = boost::mp11::mp_list<  
            _row < Empty , play        , Playing       >,  
            _row < Playing , stop        , Empty       >  
    > ;  
  
```  
  
With type-alias it also works for other types boost::mpl::vector and boost::fustion::vector.  
  
I am not sure what this inheritance is for, but all examples from boost::msm defines this transition tables that way.  
  
The problem with `boost::mp11::mp_list` is probably here <boost/mp11/mpl.hpp> - but I am not sure that.  
  
I asked the question about that on SO: https://stackoverflow.com/questions/68195912/boostmp11mp-list-cant-define-proper-transition-table-for-fsm-based-on-boost - but not get much attention.   
  
You can play with the problem on compiler explorer: https://godbolt.org/z/jTEnxPMTj  
  
The problem is not related to version of boost or compiler.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-07-02 15:16:49 UTC  
> Url: https://github.com/boostorg/mp11/issues/63#issuecomment-873074809  

While types derived from `mpl::vector<>` and `fusion::vector<>` are also MPL sequences (whether accidentally or by design, I'm not quite sure), types derived from `mp_list` aren't. See f.ex. https://godbolt.org/z/71sGzP4zj. Only `mp_list<>` itself is an MPL sequence. So `using` (or `typedef`) is correct.  
  
I also don't know why the MSM examples use inheritance.

---

## Comment 2

> Username: PiotrNycz  
> Created at: 2021-07-04 13:59:24 UTC  
> Url: https://github.com/boostorg/mp11/issues/63#issuecomment-873596709  

It seems that `boost::mp11:mp_list` is simple type defined as:  
```  
template <typename ...T> struct mp_list {};  
```  
  
while both fusion and mpl vectors are types referring to itself - in mp_list it could be something as:  
  
```  
template <typename ...T> struct mp_list {  
   using mp_list_type = mp_list;  
};  
  
```   
  
thus it would be possible to define mpl adapters working also with types derived from type-list type.  
  
I also found in fusion-mpl adapters, that it is also required some inner-type for tag used in mpl adapters, in our example something like this:  
  
```  
struct mp_list_mpl_tag;  
template <typename ...T> struct mp_list {  
   using mp_list_type = mp_list;  
   using mp_list_tag = ::boost::mp11::mp_list_mpl_tag;  
};  
  
```   
  
I also tried at build-bench.com whether inheritance is somehow faster in compilation - and it is really faster but almost impossible to notice the difference, it is so small - see https://build-bench.com/b/kslOjZyqZVmCm6qrMEgf1ptanbM  
  
```  
namespace some  
{  
struct type_list_tag;  
  
template <typename ...T>  
struct type_list  
{  
    using type = type_list;  
    using tag = type_list_tag;  
};  
  
template <typename T>  
struct front_impl;  
  
template <template <class...> class TL, typename T1, typename ...T>  
struct front_impl<TL<T1, T...>>  
{  
    using type = T1;  
};  
  
template <typename T>  
struct front  
{  
    using type = typename front_impl<typename T::type>::type;  
};  
  
template <typename T>  
using front_t = typename front<T>::type;  
  
template <typename T>  
struct pop_front_impl;  
  
template <template <class...> class TL, typename T1, typename ...T>  
struct pop_front_impl<TL<T1, T...>>  
{  
    using type = TL<T...>;  
};  
  
template <typename T>  
struct pop_front  
{  
    using type = typename pop_front_impl<typename T::type>::type;  
};  
  
template <typename T>  
using pop_front_t = typename pop_front<T>::type;  
  
}  
  
#if USE_INHERITANCE  
struct test : some::type_list<int,float,int,float,int,float> {};  
#else  
using test = some::type_list<int,float,int,float,int,float>;  
#endif  
  
int main() {  
    using namespace some;  
    front_t<pop_front_t<pop_front_t<test>>> a = 5;  
    front_t<pop_front_t<pop_front_t<pop_front_t<test>>>> b = 4;  
      
    return a + b;  
}  
```

---

## Comment 3

> Username: PiotrNycz  
> Created at: 2022-01-10 14:22:11 UTC  
> Url: https://github.com/boostorg/mp11/issues/63#issuecomment-1008920186  

Maybe the best solution to this problem would be to create `boost::mp11::mp_list2` just to be used (with inheritance) in boost::msm transition table.   
PoC is here https://wandbox.org/permlink/ciX5jWNdWTPLnGVF  
  
The idea in short:  
  
```  
namespace boost::mp11 {  
  
namespace aux {  struct mp11_tag2; }  
  
template <typename ...T>  
struct mp_list2   
{   
     using tag = aux::mp11_tag2; // mpl will catch this nested tag (see boost::mpl::sequence_tag generic impl)  
     using base = mp_list<T...>; // this will be accessible from derived classes  
      
      // the below line is not really needed - we can use aux::mp11_tag instead  
      // I just added it -- in case someone invents idea to have different tags for different type packs  
      using base_tag = typename ::boost::mpl::sequence_tag<base>::type;  
        
};  
}  
```  
And just forward all mpl operations/algorithms to  base mp_list  
  
```  
namespace boost::mpl  
{  
// at  
   
template<> struct at_impl<::boost::mp11::aux::mp11_tag2>  
{  
    template<class L, class I>   
    struct apply   
    {  
        using type = typename at_impl<typename L::base_tag>  
            ::template apply<typename L::base, I>  
            ::type;  
    };  
};  
   
// back  
//...  
  
  
```
