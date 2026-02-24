# #87 - boost::any stopped working as Kleene event in 1.86 in boost::msm [Closed]

> Username: PiotrNycz  
> Created at: 2025-03-20 09:00:23 UTC  
> Updated at: 2025-10-14 19:27:45 UTC  
> Closed at: 2025-10-14 19:27:45 UTC  
> Url: https://github.com/boostorg/msm/issues/87  

boost::any starts having compilation errors when used as "Kleene" (any) event (as described in this doc:  https://www.boost.org/doc/libs/1_86_0/libs/msm/doc/HTML/ch03s03.html#d0e1392  
  
I basically need to use "const boost::any" and mark "const boost::any" as Kleene event  
  
  
In version with "boost::any" (non const)  (the full code at the end)  
  
```cpp  
 struct transition_table : mpl::vector<  
        msmf::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false>>,  
        msmf::Row<State<1>,  boost::any, State<23>, Action<303>, Guard<true>>  
           
    > {};  
```  
  
I have this output when using boost 1.84:  
  
> State::entry(1)  
State::exit(1)  
Action(303)  
State::entry(23)  
  
  
But when switching to 1.86 - the code stops compiling:  
  
> g++ -Iboost_1_86_0 .\test.cpp  
In file included from boost_1_86_0/boost/msm/back/state_machine.hpp:65,  
                 from .\test.cpp:1:  
boost_1_86_0/boost/msm/back/dispatch_table.hpp: In instantiation of 'static boost::msm::back::HandledEnum boost::msm::back::dispatch_table<Fsm, Stt, Event, CompilePolicy>::chain_row<Seq, AnEvent, State>::execute_helper::execute(Fsm&, int, int, Event&, const mpl_::false_&) [with Sequence = boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::vector<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, 0>; Seq = boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::vector<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, 0>; AnEvent = Event<3>; State = State<1>; Fsm = boost::msm::back::state_machine<SimpleFront>; Stt = boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 1>, 1>; Event = Event<3>; CompilePolicy = boost::msm::back::favor_runtime_speed; mpl_::false_ = mpl_::bool_<false>]':  
boost_1_86_0/boost/msm/back/dispatch_table.hpp:96:57:   required from 'static boost::msm::back::HandledEnum boost::msm::back::dispatch_table<Fsm, Stt, Event, CompilePolicy>::chain_row<Seq, AnEvent, State>::execute(Fsm&, int, int, Event&) [with Seq = boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::vector<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, 0>; AnEvent = Event<3>; State   
= State<1>; Fsm = boost::msm::back::state_machine<SimpleFront>; Stt = boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 1>, 1>; Event = Event<3>; CompilePolicy = boost::msm::back::favor_runtime_speed]'  
boost_1_86_0/boost/msm/back/dispatch_table.hpp:182:50:   required from 'typename boost::disable_if<typename boost::is_same<typename Transition::current_state_type, Fsm>::type, void>::type boost::msm::back::dispatch_table<Fsm, Stt, Event, CompilePolicy>::init_cell::init_event_base_case(const Transition&, const mpl_::true_&, const mpl_::false_&) const [with Transition = boost::msm::back::dispatch_table<boost::msm::back::state_machine<SimpleFront>, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>,   
Guard<false> > >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 1>, 1>, Event<3>, boost::msm::back::favor_runtime_speed>::chain_row<boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::vector<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, 0>, Event<3>, State<1> >; Fsm = boost::msm::back::state_machine<SimpleFront>; Stt = boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 1>, 1>; Event = Event<3>; CompilePolicy = boost::msm::back::favor_runtime_speed; typename boost::disable_if<typename boost::is_same<typename Transition::current_state_type, Fsm>::type, void>::type = void; typename boost::is_same<typename Transition::current_state_type, Fsm>::type = boost::integral_constant<bool, false>; typename Transition::current_state_type = State<1>; mpl_::true_ = mpl_::bool_<true>; mpl_::false_ = mpl_::bool_<false>]'  
boost_1_86_0/boost/msm/back/dispatch_table.hpp:268:33:   required from 'typename boost::disable_if<typename has_not_real_row_tag<Transition>::type, void>::type boost::msm::back::dispatch_table<Fsm, Stt, Event, CompilePolicy>::init_cell::operator()(const Transition&, boost::msm::back::dummy<1>) const [with Transition = boost::msm::back::dispatch_table<boost::msm::back::state_machine<SimpleFront>, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 1>, 1>, Event<3>, boost::msm::back::favor_runtime_speed>::chain_row<boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::vector<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, 0>, Event<3>, State<1> >; Fsm = boost::msm::back::state_machine<SimpleFront>; Stt = boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 1>, 1>; Event = Event<3>; CompilePolicy = boost::msm::back::favor_runtime_speed; typename boost::disable_if<typename has_not_real_row_tag<Transition>::type, void>::type = void; typename has_not_real_row_tag<Transition>::type = mpl_::bool_<false>]'  
boost_1_86_0/boost/mpl/for_each.hpp:78:26:   required from 'static void boost::mpl::aux::for_each_impl<false>::execute(Iterator*, LastIterator*, TransformFunc*, F) [with Iterator = boost::mpl::v_iter<boost::mpl::v_item<boost::msm::back::dispatch_table<boost::msm::back::state_machine<SimpleFront>, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 1>, 1>, Event<3>, boost::msm::back::favor_runtime_speed>::chain_row<boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::vector<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, 0>, Event<3>, State<1> >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 0>, 0>, 1>; LastIterator = boost::mpl::v_iter<boost::mpl::v_item<boost::msm::back::dispatch_table<boost::msm::back::state_machine<SimpleFront>, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 1>, 1>, Event<3>, boost::msm::back::favor_runtime_speed>::chain_row<boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::vector<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na,   
mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, 0>, Event<3>, State<1> >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 0>, 0>, 2>; TransformFunc = boost::mpl::identity<mpl_::na>; F = boost::msm::back::dispatch_table<boost::msm::back::state_machine<SimpleFront>, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 1>, 1>, Event<3>, boost::msm::back::favor_runtime_speed>::init_cell]'  
boost_1_86_0/boost/mpl/for_each.hpp:82:22:   required from 'static void boost::mpl::aux::for_each_impl<false>::execute(Iterator*, LastIterator*, TransformFunc*, F) [with Iterator = boost::mpl::v_iter<boost::mpl::v_item<boost::msm::back::dispatch_table<boost::msm::back::state_machine<SimpleFront>, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 1>, 1>, Event<3>, boost::msm::back::favor_runtime_speed>::chain_row<boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::vector<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, 0>, Event<3>, State<1> >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 0>, 0>, 0>; LastIterator = boost::mpl::v_iter<boost::mpl::v_item<boost::msm::back::dispatch_table<boost::msm::back::state_machine<SimpleFront>, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 1>, 1>, Event<3>, boost::msm::back::favor_runtime_speed>::chain_row<boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::vector<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na,   
mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, 0>, Event<3>, State<1> >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 0>, 0>, 2>; TransformFunc = boost::mpl::identity<mpl_::na>; F = boost::msm::back::dispatch_table<boost::msm::back::state_machine<SimpleFront>, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false> > >, boost::mpl::v_item<boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >, boost::mpl::vector0<mpl_::na>, 1>, 1>, Event<3>, boost::msm::back::favor_runtime_speed>::init_cell]'  
boost_1_86_0/boost/mpl/for_each.hpp:105:18:   [ skipping 5 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
boost_1_86_0/boost/msm/back/state_machine.hpp:2161:49:   required from 'void boost::msm::back::state_machine<A0, A1, A2, A3, A4>::region_processing_helper<StateType, typename boost::enable_if<boost::mpl::is_sequence<typename StateType::initial_state> >::type>::process(const Event&) [with Event = Event<3>; StateType = SimpleFront; A0 = SimpleFront; A1 = boost::parameter::void_; A2 = boost::parameter::void_; A3 = boost::parameter::void_; A4 = boost::parameter::void_; typename boost::enable_if<boost::mpl::is_sequence<typename StateType::initial_state> >::type = void; typename StateType::initial_state = boost::mpl::vector<State<1> >]'  
boost_1_86_0/boost/msm/back/state_machine.hpp:2224:23:   required from 'boost::msm::back::HandledEnum boost::msm::back::state_machine<A0, A1, A2, A3, A4>::do_process_event(const Event&, bool) [with Event = Event<3>; A0 = SimpleFront; A1 = boost::parameter::void_; A2 = boost::parameter::void_; A3 = boost::parameter::void_; A4 = boost::parameter::void_]'  
boost_1_86_0/boost/msm/back/state_machine.hpp:1925:42:   required from 'boost::msm::back::HandledEnum boost::msm::back::state_machine<A0, A1, A2, A3, A4>::do_process_helper(const EventType&, const mpl_::false_&, bool) [with StateType = Event<3>; EventType = Event<3>; A0 = SimpleFront; A1 = boost::parameter::void_; A2 = boost::parameter::void_; A3 = boost::parameter::void_; A4 = boost::parameter::void_; mpl_::false_ = mpl_::bool_<false>]'  
boost_1_86_0/boost/msm/back/state_machine.hpp:2191:65:   required from 'boost::msm::back::execute_return boost::msm::back::state_machine<A0, A1, A2, A3, A4>::process_event_internal(const Event&,   
boost::msm::back::EventSource) [with Event = Event<3>; A0 = SimpleFront; A1 = boost::parameter::void_; A2 = boost::parameter::void_; A3 = boost::parameter::void_; A4 = boost::parameter::void_; boost::msm::back::execute_return = boost::msm::back::HandledEnum; boost::msm::back::EventSource = unsigned char]'  
boost_1_86_0/boost/msm/back/state_machine.hpp:1270:38:   required from 'boost::msm::back::execute_return boost::msm::back::state_machine<A0, A1, A2, A3, A4>::process_event(const Event&) [with Event = Event<3>; A0 = SimpleFront; A1 = boost::parameter::void_; A2 = boost::parameter::void_; A3 = boost::parameter::void_; A4 = boost::parameter::void_; boost::msm::back::execute_return = boost::msm::back::HandledEnum]'  
.\test.cpp:84:22:   required from here  
boost_1_86_0/boost/msm/back/dispatch_table.hpp:75:54: error: cannot bind non-const lvalue reference of type 'boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >::transition_event&' {aka 'boost::any&'} to an rvalue of type 'boost::msm::back::state_machine<SimpleFront>::row_<boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> > >::transition_event' {aka 'boost::any'}  
   75 |                  HandledEnum res = first_row::execute(fsm,region_index,state,evt);  
      |                                    ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from boost_1_86_0/boost/msm/back/state_machine.hpp:49:  
boost_1_86_0/boost/any.hpp:93:9: note:   after user-defined conversion: 'boost::any::any(ValueType&&, typename std::enable_if<(! std::is_same<boost::any&, ValueType>::value)>::type*, typename std::enable_if<(! std::is_const<_Tp>::value)>::type*) [with ValueType = Event<3>&; typename std::enable_if<(! std::is_same<boost::any&, ValueType>::value)>::type = void; typename std::enable_if<(! std::is_const<_Tp>::value)>::type = void]'  
   93 |         any(ValueType&& value  
      |         ^~~  
boost_1_86_0/boost/msm/back/state_machine.hpp:437:100: note:   initializing argument 4 of 'static boost::msm::back::HandledEnum boost::msm::back::state_machine<A0, A1, A2, A3, A4>::row_<ROW>::execute(boost::msm::back::state_machine<A0, A1, A2, A3, A4>::library_sm&, int, int, transition_event&) [with ROW = boost::msm::front::Row<State<1>, boost::any, State<23>, Action<303>, Guard<true> >; A0 = SimpleFront; A1 = boost::parameter::void_; A2 = boost::parameter::void_; A3 = boost::parameter::void_; A4 = boost::parameter::void_; boost::msm::back::state_machine<A0, A1, A2, A3, A4>::library_sm = boost::msm::back::state_machine<SimpleFront>; transition_event = boost::any]'  
  437 |         static HandledEnum execute(library_sm& fsm, int region_index, int state, transition_event& evt)  
      |                              
  
  
What helps is to add const next to boost::any in transition_table and mark "const boost::any" as Kleene event,  
the code that works now:  
  
```cpp  
#include <boost/msm/back/state_machine.hpp>  
#include <boost/msm/front/state_machine_def.hpp>  
#include <boost/msm/front/functor_row.hpp>  
#include <boost/msm/front/euml/operator.hpp>  
#include <boost/any.hpp>  
  
namespace msm = boost::msm;  
namespace msmf = msm::front;  
namespace msmb = msm::back;  
namespace msmfe = boost::msm::front::euml;  
namespace mpl = boost::mpl;  
  
  
template <bool Value>  
struct Guard  
{  
    template <typename Event, typename Fsm, typename SourceState, typename TargetState> \  
    bool operator()(const Event&, Fsm&, SourceState&, TargetState&) \  
    {  
        return Value;  
    }  
};  
  
template <int E>   
struct Event{};  
  
template <int I>  
struct Action  
{  
    template <typename Event, typename Fsm, typename SourceState, typename TargetState>  
    void operator()(const Event&, Fsm&, SourceState&, TargetState&)  
    {  
        std::cout << "Action(" << I << ")" << '\n';  
    }  
};  
  
template <int I>  
struct State: msmf::state<>   
{  
    template <typename Event, typename Fsm>  
    void on_entry(Event&, Fsm&)  
    {  
        std::cout << "State::entry(" << I << ")" << '\n';  
    }  
    template <typename Event, typename Fsm>  
    void on_exit(Event&, Fsm&)  
    {  
        std::cout << "State::exit(" << I << ")" << '\n';  
    }  
};  
  
  
template <int I>  
struct EndState: msmf::terminate_state<>   
{  
    template <typename Event, typename Fsm>  
    void on_entry(const Event&, Fsm&)  
    {  
        std::cout << "EndState::entry(" << I << ")" << '\n';  
    }  
    template <typename Event, typename Fsm>  
    void on_exit(const Event&, Fsm&)  
    {  
        std::cout << "EndState::exit(" << I << ")" << '\n';  
    }  
};  
  
// Since boost_1_86_0 boost::any stopped working as a Kleene event type in boost::msm  
// but const boost::any works -- only we need to mark it as a Kleene event type  
template<>   
struct boost::msm::is_kleene_event<const boost::any>  
{  
    using type = boost::mpl::true_;  
};   
  
struct SimpleFront : msmf::state_machine_def<SimpleFront>   
{  
    using Base = msmf::state_machine_def<SimpleFront>;  
  
  
    struct transition_table : mpl::vector<  
        msmf::Row<State<1>, Event<3>, State<23>, Action<3>, Guard<false>>,  
        msmf::Row<State<1>, const boost::any, State<23>, Action<303>, Guard<true>>  
           
    > {};  
  
    using initial_state = boost::mpl::vector<State<1>>;  
  
};  
  
using SimpleImpl = msmb::state_machine<SimpleFront>;  
  
int main() {  
    SimpleImpl fsm;  
    fsm.start();  
    fsm.process_event(Event<3>{});  
}  
  
  
```

---

## Comment 1

> Username: chandryan  
> Created at: 2025-10-12 15:56:41 UTC  
> Url: https://github.com/boostorg/msm/issues/87#issuecomment-3394752224  

The origin of this issue seems to date back to the introduction of `back11` in the repo: https://github.com/boostorg/msm/commit/c287cc097ea6c7afeee1c9b47e06f7671095ede8#diff-8609ca70fabeda64e1fbfe75da5348516160df6c765c137d1e4cbb301bcdae51  
  
It affects `back` and `back11`, `backmp11` seems to work:   
  
  
@henry-ch   
I'm not sure what the intention of removing the const qualifiers was, do you have more insights?  
Though in any case I assume the behavior should not change, at least in `back`.

---

## Comment 2

> Username: chandryan  
> Created at: 2025-10-14 19:27:45 UTC  
> Url: https://github.com/boostorg/msm/issues/87#issuecomment-3403294479  

I added a test case with your example. The tests pass, the issue should be fixed now.
